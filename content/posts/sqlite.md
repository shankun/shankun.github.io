+++
title = "SQLite3"
date = 2023-11-15T14:39:00+08:00
draft = false

[taxonomies]
categories = ["学而时习之"]
tags = ["sqlite3", "C++"]

[extra]
lang = "zh_CN"
toc = false
copy = true
comment = false
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
+++

<!--more-->
[An Introduction To The SQLite C/C++ Interface](https://www.sqlite.org/cintro.html)  
[sqlite3pp](https://github.com/iwongu/sqlite3pp)  
[sqlitexx](https://github.com/Rapptz/sqlitexx)  

1. 从离线包web服务批量下载b3dm静态模型
```powershell
&"C:\Program Files (x86)\Microsoft Visual Studio\Shared\Python39_64\python.exe" .\downloader.py -p muping -d d:\work\2023\3dtilesDownloader\muping\
```

2. 火鸟数据库提取到sqlite数据库
```powershell
&"C:\Program Files (x86)\Microsoft Visual Studio\Shared\Python39_64\python.exe" .\extract_fdb.py -p f:\离线包\HCityData\publish\muping\ -d d:\work\2023\3dtilesDownloader\muping\
```

{% codeblock(name="extract_fdb.py") %}
```python
#!/usr/bin/env python
# coding:utf-8

import sys
import traceback
import os
import time
import fdb
import codecs
import sqlite3
import json
import getopt
import math

class HcElement:
    pipe_fields = ['STARTCODENUM', 'ENDCODENUM', 'PIPE_BEGIN_POSITION',
                   'PIPE_END_POSITION', 'PIPEDN', 'PIPEDNH', 'PIPELENGTH', 
                   'STARTELEV','ENDELEV', 'PIPEMATERIAL', 'OWNERROAD', 
                   'ELEPRESSURE','EMBEDTYPE', 'EMBEDDATE', 'FLOWDIRECTION', 
                   'PRESSURE','PIPE_BEGIN_COORD', 'PIPE_END_COORD']
    
    node_fields = ['NODECODENUM', 'NODE_POSITION', 'NODETYPE', 'NODEDEFTYPE',
                   'GROUNDELEV', 'OWNERSHIP', 'MAPCODE', 'NODE_COORD']
    
    def __init__(self):
        self.base_props = [] # elem_id,name,extw,exte,exts,extn,extminh,extmaxh,modelname
        self.name = ''
        self.mdlcls_id = 0
        self.attrs = {}

    r""" 从属性集合中提取自己的属性，格式化成字符串赋给base_props的末元素
    [{
        "group": "xxx1",
        "items": [{
            "label": "中文名1",
            "value": 123
        }]
     },
     {
        "group": "xxx2",
        "items": [{
            "label": "中文名2",
            "value": "123"
        }]
     }
    ]
    """
    def updateAttrs(self, attributes, field_defs):
        props = {}
        nm = self.base_props[-1]
        self.attrs = attributes[self.name]
        for k,v in self.attrs.items():
            indx = next((i for i, f in enumerate(field_defs)
                        if f[0] == self.mdlcls_id and f[1] == k), None)

            alias = field_defs[indx][2] if len(field_defs) > indx else k
            props.setdefault(field_defs[indx][-1], []).append({'label':alias, 'value':v})

        if '常规属性' in props:
            props['常规属性'].append({'label':'MODELNAME', 'value':nm})

        attr_set = [{'group':k, 'items':v} for k, v in props.items()]        
        self.base_props[-1] = json.dumps(attr_set, ensure_ascii=False)

class ProCoordinateSys:
    Beijing54 = (6378245, 298.3)
    XiAn80 = (6378140, 298.257)
    WGS84 = (6378137, 298.257)
    CGCS2000 = (6378137, 298.257222101)

    def __init__(self, dSemimajorAxis, dInverseFlattening, LC, FE, FN, k0):
        self.a = dSemimajorAxis         #长半轴
        self.f = dInverseFlattening    #扁率倒数 (a/(a-b))
        self.b = self.a*(1-1/self.f)         #短半轴
        self.e = math.sqrt(1-math.pow(self.b/self.a,2))   #第一偏心率 sqrt(1-pow(b/a,2))
        self.ep = math.sqrt(math.pow(self.a/self.b,2)-1); #第二偏心率 sqrt(pow(a/b,2)-1)
        self.aSquare = self.a*self.a
        self.bSquare = self.b*self.b;      
        self.e1 = (1-self.b/self.a)/(1+self.b/self.a)
        self.jDenominator = self.a*(1-math.pow(self.e,2)/4-3*math.pow(self.e,4)/64-5*math.pow(self.e,6)/256)
        self.BfCoefficient1 = 3*self.e1/2-27*math.pow(self.e1,3)/32
        self.BfCoefficient2 = 21*math.pow(self.e1,2)/16-55*math.pow(self.e1,4)/32
        self.BfCoefficient3 = 151*math.pow(self.e1,3)/96
        self.MCoefficient1 = 1-math.pow(self.e,2)/4-3*math.pow(self.e,4)/64-5*math.pow(self.e,6)/256
        self.MCoefficient2 = 3*math.pow(self.e,2)/8+3*math.pow(self.e,4)/32+45*math.pow(self.e,6)/1024
        self.MCoefficient3 = 15*math.pow(self.e,4)/256+45*math.pow(self.e,6)/1024
        self.MCoefficient4 = 35*math.pow(self.e,6)/3072
        self.LC = LC
        self.FE = FE
        self.FN = FN
        self.k0 = k0

    # coordinate: [经度,纬度]
    def gaussToGeo(self, y: float, x: float) -> tuple:
        j = ((x-self.FN)/self.k0)/self.jDenominator
        Bf = j + self.BfCoefficient1 * math.sin(2*j)
        Bf += self.BfCoefficient2 * math.sin(4*j)
        Bf += self.BfCoefficient3 * math.sin(6*j)

        Tf = math.pow(math.tan(Bf), 2)
        Cf = math.pow(self.ep,2)*math.pow(math.cos(Bf),2)
        Rf = self.a*(1-math.pow(self.e,2))/math.pow(1-math.pow(self.e,2)*math.pow(math.sin(Bf),2), 1.5)
        Nf = self.a/math.sqrt(1-math.pow(self.e,2)*math.pow(math.sin(Bf),2))
        D = (y-self.FE)/(self.k0*Nf)

        b = Bf*180/math.pi - Nf*math.tan(Bf)/Rf * (math.pow(D,2)/2-(5+3*Tf+Cf-9*Tf*Cf)*math.pow(D,4)/24 + (61+90*Tf+45*math.pow(Tf,2))*math.pow(D,6)/720) * 180/math.pi
        l = self.LC + (D-(1+2*Tf+Cf)*math.pow(D,3)/6+(5+28*Tf+6*Cf+8*Tf*Cf+24*math.pow(Tf,2))*math.pow(D,5)/120)/math.cos(Bf) * 180/math.pi
        return l,b


# 将火鸟数据库中的模型与属性导出到sqlite
class HcDataMigration:

    def __init__(self, sqlite_db):
        # {datasrc:[模型geoclass]}，列表元素是字典 {geoclsid,type,tbl}，
        # modelclassid是列表中的索引+1
        self.geoclasses = {}
        # {datasrc:[属性geoclass]}，与模型geoclass一一对应，列表元素是字典 {geoclsid,type,tbl}
        self.attrclasses = {}
        self.isPipe = 0
        #firstly = os.path.exists(sqlite_db)
        self.sqlite_conn = sqlite3.connect(sqlite_db)
        self.sqlite_cur = self.sqlite_conn.cursor()
        self.createTables()
        # 所有字段定义
        self.field_defs = []

    # 从proj.json中读取内容
    def getModelInfo(self, proj_json):
        with codecs.open(proj_json, 'r', encoding='gb2312') as f:
            self.models = json.loads(f.read())['models']

    # 提取纹理图片
    def extractTextures(self, src_fdb, img_dir):
        # print(src_fdb)
        self.fdb_con = fdb.connect(dsn=src_fdb + '.fdb',
            user='sysdba', password='masterkey',
            charset='UTF8', utf8params=True) # 打开或切换数据源
        
        self.fdb_cur = self.fdb_con.cursor()
        if not os.path.exists(img_dir):
            os.makedirs(img_dir)

        self.fdb_cur.execute("select NAME,IMAGENAME,IMAGE from TEXTURES")
        self.fdb_cur.set_stream_blob("IMAGE")
        for (name, imgname, imgdata) in self.fdb_cur:
            filepath = img_dir + name + imgname[imgname.rindex('.'):]
            with open(filepath, 'wb') as f:
                f.write(imgdata.read())
            imgdata.close()

    # 创建sqlite中的所有表结构
    def createTables(self):
        self.sqlite_cur.execute('''create table project_model 
            (id         VARCHAR(100) constraint table_name_pk primary key not null,
            name        VARCHAR(100) not null,
            is_pipe     INTEGER not null,
            type        VARCHAR(50),
            created_at  INTEGER);''')
        
        self.sqlite_cur.execute('''create table model_class 
            (id INTEGER primary key autoincrement constraint model_class_pk not null,
            model_id    VARCHAR(100) not null,
            geo_class   INTEGER not null,
            type        VARCHAR(50) not null,
            typename    VARCHAR(100) not null);''')
        
        self.sqlite_cur.execute('''create table column_registry 
            (id INTEGER primary key autoincrement constraint column_registry_pk not null,
            model_class_id      INTEGER not null,
            field_name          VARCHAR(50) not null,
            field_displayname   VARCHAR(50) not null,
            type                INTEGER not null,
            size                INTEGER not null,
            "group"             VARCHAR(50));''')
        
        self.sqlite_cur.execute('''create table pipe_attr 
            (id INTEGER primary key autoincrement constraint pipe_attr_pk not null,
            element_id          INTEGER not null,
            model_class_id      INTEGER not null,
            STARTCODENUM        VARCHAR(100),
            ENDCODENUM          VARCHAR(100),
            PIPE_BEGIN_POSITION VARCHAR(100),
            PIPE_END_POSITION   VARCHAR(100),
            PIPEDN              INTEGER,
            PIPEDNH             INTEGER,
            PIPELENGTH          REAL,
            STARTELEV           REAL,
            ENDELEV             REAL,
            PIPEMATERIAL        VARCHAR(100),
            OWNERROAD           VARCHAR(100),
            ELEPRESSURE         VARCHAR,
            EMBEDTYPE           VARCHAR,
            EMBEDDATE           VARCHAR,
            FLOWDIRECTION       VARCHAR,
            PRESSURE            REAL,
            PIPE_BEGIN_COORD    VARCHAR(100),
            PIPE_END_COORD      VARCHAR(100));''')
        
        self.sqlite_cur.execute('''create table pipe_node_attr 
            (id INTEGER primary key autoincrement constraint pipe_node_attr_pk not null,
            element_id      INTEGER not null,
            model_class_id  INTEGER not null,
            NODECODENUM     VARCHAR(100),
            NODE_POSITION   VARCHAR(100),
            NODETYPE        VARCHAR(100),
            NODEDEFTYPE     VARCHAR(100),
            GROUNDELEV      REAL,
            OWNERSHIP       VARCHAR(100),
            MAPCODE         VARCHAR(100),
            NODE_COORD      VARCHAR(100));''')

        self.sqlite_cur.execute('''create table element 
            (id INTEGER primary key autoincrement constraint element_pk not null,
            element_id      INTEGER not null,
            model_class_id  INTEGER not null,
            extentw         REAL,
            extente         REAL,
            extents         REAL,
            extentn         REAL,
            extentminh      REAL,
            extentmaxh      REAL,
            attr            TEXT);''')

    # 模型所属子图层，来源于fdb的DATACLASS表
    def fill_model_class(self, ds):
        self.fdb_cur.execute('''select OBJID,TABLENAME,TYPE,TYPENAME,MODELREF 
            from DATACLASS where TYPENAME is not null''')
        
        data_rows = []
        for id,table,type,typename,modelref in self.fdb_cur.fetchall():
            newitem = {'geoclsid':id, 'type':type, 'tbl':table}
            if modelref is None: # model_class表中只有模型geoclass
                data_rows.append((ds, id, type, typename))
                self.geoclasses.setdefault(ds, []).append(newitem) # 包含属性geoclass
            else:
                self.attrclasses.setdefault(ds, []).append(newitem)

        sqlstatment = '''insert into model_class 
            (model_id,geo_class,type,typename) values(?, ?, ?, ?)'''
        self.sqlite_cur.executemany(sqlstatment, data_rows)

    # 属性字段定义，来源于fdb的GEO_COLUM_REGISTRY表
    def fill_column_registry(self, ds):
        self.fdb_cur.execute('''select 
            GEOCLASS,FIELDNAME,ALIASNAME,TYPE,SIZE,"GROUP" 
            from GEO_COLUM_REGISTRY''')

        for row in self.fdb_cur.fetchall():
            indx = next((i for i, x in enumerate(self.attrclasses[ds])
                         if x['geoclsid'] == row[0]), None)
            # 属性geoclassId转modelclassId
            new_row = list(row)
            new_row[0] = indx + 1
            self.field_defs.append(tuple(new_row))

        sqlstatment = '''insert into column_registry 
            (model_class_id,field_name,field_displayname,type,size,"group") 
            values(?, ?, ?, ?, ?, ?)'''
        self.sqlite_cur.executemany(sqlstatment, self.field_defs)

    # 获取模型对应的属性，存成字典，只适用于管线，因为列名即字段名，
    def fetch_attrs(self, datasrc, index, attrs):
        table = self.attrclasses[datasrc][index]['tbl']
        statment = '''SELECT Fields.RDB$FIELD_NAME 
                    "Column Name" FROM RDB$RELATION_FIELDS 
                    Fields WHERE Fields.RDB$RELATION_NAME = \'''' # 必须用单引号

        statment += table + "' and Fields.RDB$SYSTEM_FLAG = 0"
        self.fdb_cur.execute(statment)
        columns = [ col[0].strip() for col in self.fdb_cur.fetchall() ]
        att_bgn = columns.index('EXTENTMAXH') + 1 # 从这里之后的列都是属性
        fields = ['REFMODELNAME'] + columns[att_bgn:]
        statment = 'select ' + ','.join(fields) + ' from '
        self.fdb_cur.execute(statment + table)

        for row in self.fdb_cur.fetchall():
            attrs[row[0]] = dict(zip(columns[att_bgn:], list(row[1:])))
    
    def fill_element(self, ds):
        statemnt = '''select OBJID,NAME,EXTENTW,EXTENTE,EXTENTS,EXTENTN,
            EXTENTMINH,EXTENTMAXH,MODELNAME from '''

        data_rows = []
        for i,mdl_gcls in enumerate(self.geoclasses[ds]):
            is_node = (self.isPipe == 1) and mdl_gcls['type'].endswith('_Node')
            name2Attrs = {} # name到属性集的映射
            self.fetch_attrs(ds, i, name2Attrs)
            self.fdb_cur.execute(statemnt + mdl_gcls['tbl'])
            elements = []
            for row in self.fdb_cur.fetchall():
                elem = HcElement()
                elem.mdlcls_id = i + 1
                elem.name = row[1]
                elem.base_props = list(row)
                elem.base_props[1] = elem.mdlcls_id
                elem.updateAttrs(name2Attrs, self.field_defs)
                elements.append(elem)
                data_rows.append(tuple(elem.base_props))
            
            if self.isPipe == 1:
                if is_node:
                    self.fill_pipe_node_attr(ds, elements)
                else:
                    self.fill_pipe_attr(ds, elements)

        sql_ins = '''insert into element (element_id,model_class_id,
            extentw,extente,extents,extentn,extentminh,extentmaxh,attr) 
            values(?, ?, ?, ?, ?, ?, ?, ?, ?)'''

        self.sqlite_cur.executemany(sql_ins, data_rows)
    
    # 若管径PIPEDN是宽x高的格式，则将高度写入PIPEDNH
    def fill_pipe_attr(self, dtsrc, pipes):
        data_rows = []
        for pipe in pipes:
            if 'PIPEDN' in pipe.attrs:
                valstr = pipe.attrs.get('PIPEDN').upper()
                if 'X' in valstr:
                    width, height = valstr.split('X')
                    pipe.attrs['PIPEDN'] = int(width)
                    pipe.attrs['PIPEDNH'] = int(height)

            row = [pipe.base_props[0], pipe.mdlcls_id]
            row += [pipe.attrs.get(key) for key in HcElement.pipe_fields]
            # 将PIPE_BEGIN_POSITION高斯坐标换算成经纬度
            position = pipe.attrs.get('PIPE_BEGIN_POSITION').split(',')
            l, b = self.pro_cs.gaussToGeo(float(position[0]), float(position[1]))
            row[-2] = "{:.9f},{:.9f}".format(l, b)
            # 将PIPE_END_POSITION高斯坐标换算成经纬度
            position = pipe.attrs.get('PIPE_END_POSITION').split(',')
            l, b = self.pro_cs.gaussToGeo(float(position[0]), float(position[1]))
            row[-1] = "{:.9f},{:.9f}".format(l, b)
            data_rows.append(row)

        sql_ins = "insert into pipe_attr (element_id,model_class_id,"
        sql_ins += ",".join(HcElement.pipe_fields) + ")"
        placeholder = ['?'] * (2 + len(HcElement.pipe_fields))
        sql_ins += "values(" + ",".join(placeholder) + ")"
        self.sqlite_cur.executemany(sql_ins, data_rows)
    
    def fill_pipe_node_attr(self, dtsrc, nodes):
        data_rows = []
        for node in nodes:
            row = [node.base_props[0], node.mdlcls_id]
            row += [node.attrs.get(key) for key in HcElement.node_fields]
            position = node.attrs.get('NODE_POSITION').split(',')
            # 将NODE_POSITION高斯坐标换算成经纬度
            l, b = self.pro_cs.gaussToGeo(float(position[0]), float(position[1]))
            row[-1] = "{:.9f},{:.9f}".format(l, b)
            data_rows.append(row)

        sql_ins = "insert into pipe_node_attr (element_id,model_class_id,"
        sql_ins += ",".join(HcElement.node_fields) + ")"
        placeholder = ['?'] * (2 + len(HcElement.node_fields))
        sql_ins += "values(" + ",".join(placeholder) + ")"
        self.sqlite_cur.executemany(sql_ins, data_rows)
    
    # 保存sqlite
    def saveToSqliteDB(self, lyr_name: str, data_src: str):
        # self.fdb_cur.execute('select "DATE" from DATAOPLOG order by OBJID desc')
        # lastdate = self.fdb_cur.fetchone()[0]
        lastdate = self.models[lyr_name][data_src]['date']
        self.isPipe = 1 if self.models[lyr_name]['Datatype'] == 'pipe' else 0
        mdltype = self.models[lyr_name]['Datatype'] + '_'
        mdltype += self.models[lyr_name]['Datafrom']
        statement = "insert into project_model values(?, ?, ?, ?, ?)"
        date_time = time.mktime(time.strptime(lastdate, '%Y-%m-%d %H:%M:%S'))

        self.sqlite_cur.execute(statement, 
            [data_src, lyr_name, self.isPipe, mdltype, date_time])

        self.fill_model_class(data_src)
        self.fill_column_registry(data_src)

        self.fdb_cur.execute('select DEFINE from COORDINATE_SYSTEM')
        self.fdb_cur.set_stream_blob("DEFINE")
        reader = self.fdb_cur.fetchone()[0]
        header = reader.read(4 + 8) # 跳过4字节magic和8字节ver
        s = str(reader.read(), 'UTF-8')
        cs_def = json.loads(s) # 每个数据源仅有一个入库坐标系参数
        reader.close()
        self.pro_cs = ProCoordinateSys(cs_def['SemimajorAxis'], 
                                       cs_def['InverseFlattening'], 
                                       cs_def['CentralMeridian'],
                                       cs_def['FalseEasting'],
                                       cs_def['FalseNorthing'],
                                       cs_def['ScaleFactor'])

        self.fill_element(data_src)
        self.sqlite_conn.commit()
        self.fdb_cur.close()
        self.fdb_con.close()
    
def help():
    print("使用说明：从托盘退出离线包程序，再执行python脚本。")
    print("     ", "options:")
    print("          ", "--proj  / -p    需要下载3dtiles数据的项目目录，必填")
    print("          ", "--dir   / -d    输出目录，必填")

    return

if __name__ == "__main__":
    prjFolder = ''
    savedir = ''
    startTime = time.time()

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:p:d:", ["help", "proj=", "dir="])
    except getopt.GetoptError:
        print('param error, use -h')
        sys.exit(2)

    for opt, arg in opts:
        if opt in ('-h', "--help"):
            help()
            sys.exit()
        elif opt in ("-p", "--proj"):
            prjFolder = arg
        elif opt in ("-d", "--dir"):
            savedir = arg

    if prjFolder == '':
        print('please input proj param or use --help to see how to use it')
        sys.exit(2)
    if savedir == '':
        print('please input dir param or use --help to see how to use it')
        sys.exit(2)

    if os.path.isfile(savedir) or not os.path.exists(savedir):
        print('savedir must be a valid directory, not ', savedir)
        sys.exit(2)

    if savedir[-1] != '\\':
        savedir += '\\'
    if prjFolder[-1] != '\\':
        prjFolder += '\\'

    with open(savedir + 'layersdict.json', 'r', encoding='utf-8') as f:
        dict_lyrs = json.loads(f.read())
    
    db_trans = HcDataMigration(savedir + 'proj.db')
    db_trans.getModelInfo(prjFolder + 'proj.json')
    for key,each_fdb in dict_lyrs.items():
        db_trans.extractTextures(prjFolder + each_fdb, savedir + 'images\\')
        db_trans.saveToSqliteDB(key, each_fdb)

    print("迁移完成, 总耗时：", str(int(time.time() - startTime)), 's')
```
{% end %}