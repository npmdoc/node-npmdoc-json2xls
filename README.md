# api documentation for  [json2xls (v0.1.2)](https://github.com/rikkertkoppes/json2xls#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-json2xls.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-json2xls) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-json2xls.svg)](https://travis-ci.org/npmdoc/node-npmdoc-json2xls)
#### canonically transform json to an excel document

[![NPM](https://nodei.co/npm/json2xls.png?downloads=true)](https://www.npmjs.com/package/json2xls)

[![apidoc](https://npmdoc.github.io/node-npmdoc-json2xls/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-json2xls_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-json2xls/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-json2xls/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-json2xls/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Rikkert Koppes"
    },
    "bugs": {
        "url": "https://github.com/rikkertkoppes/json2xls/issues"
    },
    "dependencies": {
        "excel-export": "~0.3.11"
    },
    "description": "canonically transform json to an excel document",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "37358ed82a8fcaf92bb3b24d8759ac57e394e017",
        "tarball": "https://registry.npmjs.org/json2xls/-/json2xls-0.1.2.tgz"
    },
    "gitHead": "db90c6bac777de49edcad3e9eea4b9585917f2cf",
    "homepage": "https://github.com/rikkertkoppes/json2xls#readme",
    "license": "BSD-2-Clause",
    "main": "lib/json2xls.js",
    "maintainers": [
        {
            "name": "rikkertkoppes",
            "email": "rikkert@rikkertkoppes.com"
        }
    ],
    "name": "json2xls",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/rikkertkoppes/json2xls.git"
    },
    "scripts": {
        "test": "jasmine-node spec"
    },
    "version": "0.1.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module json2xls](#apidoc.module.json2xls)
1.  [function <span class="apidocSignatureSpan">json2xls.</span>middleware (req, res, next)](#apidoc.element.json2xls.middleware)
1.  [function <span class="apidocSignatureSpan">json2xls.</span>prepareJson (json, config)](#apidoc.element.json2xls.prepareJson)



# <a name="apidoc.module.json2xls"></a>[module json2xls](#apidoc.module.json2xls)

#### <a name="apidoc.element.json2xls.middleware"></a>[function <span class="apidocSignatureSpan">json2xls.</span>middleware (req, res, next)](#apidoc.element.json2xls.middleware)
- description and source-code
```javascript
middleware = function (req, res, next) {
    res.xls = function(fn,data,config) {
        var xls = transform(data,config);
        res.setHeader('Content-Type', 'application/vnd.openxmlformats');
        res.setHeader("Content-Disposition", "attachment; filename=" + fn);
        res.end(xls, 'binary');
    };
    next();
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.json2xls.prepareJson"></a>[function <span class="apidocSignatureSpan">json2xls.</span>prepareJson (json, config)](#apidoc.element.json2xls.prepareJson)
- description and source-code
```javascript
prepareJson = function (json, config) {
    var res = {};
    var conf = config||{};
    var jsonArr = [].concat(json);
    var fields = conf.fields || Object.keys(jsonArr[0]||{});
    var types = [];
    if (!(fields instanceof Array)) {
        types = Object.keys(fields).map(function(key) {
            return fields[key];
        });
        fields = Object.keys(fields);
    }
    //cols
    res.cols = fields.map(function(key,i) {
        return {
            caption: key,
            type: getType(jsonArr[0][key],types[i]),
            beforeCellWrite: function(row, cellData, eOpt){
                eOpt.cellType = getType(cellData,types[i]);
                return cellData;
            }
        };
    });
    //rows
    res.rows = jsonArr.map(function(row) {
        return fields.map(function(key) {
            var value = getByString(row,key);
            //stringify objects
            if(value && value.constructor == Object) value = JSON.stringify(value);
            //replace illegal xml characters with a square
            //see http://www.w3.org/TR/xml/#charsets
            //#x9 | #xA | #xD | [#x20-#xD7FF] | [#xE000-#xFFFD] | [#x10000-#x10FFFF]
            if (typeof value === 'string') {
                value = value.replace(/[^\u0009\u000A\u000D\u0020-\uD7FF\uE000-\uFFFD\u10000-\u10FFFF]/g,'');
            }
            return value;
        });
    });
    //add style xml if given
    if (conf.style) {
        res.stylesXmlFile = conf.style;
    }
    return res;
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
