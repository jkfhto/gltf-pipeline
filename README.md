# glTF Pipeline

[![License](https://img.shields.io/:license-apache-blue.svg)](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/LICENSE.md)
[![Build Status](https://travis-ci.org/AnalyticalGraphicsInc/gltf-pipeline.svg?branch=master)](https://travis-ci.org/AnalyticalGraphicsInc/gltf-pipeline)
[![Coverage Status](https://coveralls.io/repos/AnalyticalGraphicsInc/gltf-pipeline/badge.svg?branch=master)](https://coveralls.io/r/AnalyticalGraphicsInc/gltf-pipeline?branch=master)

<p align="center">
<a href="https://www.khronos.org/gltf"><img src="doc/gltf.png" onerror="this.src='gltf.png'"/></a>
</p>

Content pipeline tools for optimizing [glTF](https://www.khronos.org/gltf) assets by [Richard Lee](http://leerichard.net/) and the [Cesium team](http://cesiumjs.org/).

Richard Lee和Cesium团队优化glTF文件的内容管道工具

Supports common operations including:  支持常见操作，包括
* Converting glTF to glb (and reverse)  

  将glTF转换为glb（和反转）
* Saving buffers/textures as embedded or separate files

  将缓冲区/纹理嵌入到gltf或保存到单独的文件
* Converting glTF 1.0 models to glTF 2.0 (using the [KHR_technique_webgl](https://github.com/KhronosGroup/glTF/tree/master/extensions/2.0/Khronos/KHR_technique_webgl) extension)

  将glTF 1.0模型转换为glTF 2.0（使用KHR_technique_webgl扩展）

TODO: KHR_techniques_webgl - fix name in link once https://github.com/KhronosGroup/glTF/pull/1296 is merged

`gltf-pipeline` can be used as a command-line tool or Node.js module.

## Getting Started  入门

Install [Node.js](https://nodejs.org/en/) if you don't already have it, and then:

如果你还没有安装Node.js，请安装：
```
npm install
```

### Using gltf-pipeline as a command-line tool:  

使用gltf-pipeline作为命令行工具

#### Converting a glTF to glb  

将glTF转换为glb

`node bin/gltf-pipeline.js -i model.gltf -o model.glb`

`node bin/gltf-pipeline.js -i model.gltf -b`

#### Converting a glb to glTF

将glb转换为glTF

`node bin/gltf-pipeline.js -i model.glb -o model.gltf`

`node bin/gltf-pipeline.js -i model.glb -j`

#### Converting a glTF to Draco glTF   

将glTF转换为Draco glTF

`node bin/gltf-pipeline.js -i model.gltf -d -s -o modelDraco.gltf`

### Saving separate textures   

保存单独的纹理

`node bin/gltf-pipeline.js -i model.gltf -t`

### Using gltf-pipeline as a library:   

使用gltf-pipeline作为库：

#### Converting a glTF to glb:  

将glTF转换为glb

```javascript
var gltfPipeline = require('gltf-pipeline');
var fsExtra = require('fs-extra');
var gltfToGlb = gltfPipeline.gltfToGlb;
var gltf = fsExtra.readJsonSync('model.gltf');
gltfToGlb(gltf)
    .then(function(results) {
        fsExtra.writeFileSync('model.glb', results.glb);
    }
```

#### Converting a glb to glTF

将glb转换为glTF

```javascript
var gltfPipeline = require('gltf-pipeline');
var fsExtra = require('fs-extra');
var glbToGltf = gltfPipeline.glbToGltf;
var glb = fsExtra.readFileSync('model.glb');
glbToGltf(glb)
    .then(function(results) {
        fsExtra.writeJsonSync('model.gltf', results.gltf);
    }
```

#### Saving separate textures

保存单独的纹理

```javascript
var gltfPipeline = require('gltf-pipeline');
var fsExtra = require('fs-extra');
var gltfToGlb = gltfPipeline.gltfToGlb;
var gltf = fsExtra.readJsonSync('model.gltf');
var options = {
    separateTextures: true
};
processGltf(gltf, options)
    .then(function(results) {
        fsExtra.writeJsonSync('model.gltf', results.gltf);
        // Save separate resources
        var separateResources = results.separateResources;
        for (var relativePath in separateResources) {
            if (separateResources.hasOwnProperty(relativePath)) {
                var resource = separateResources[relativePath];
                fsExtra.writeFileSync(relativePath, resource));
            }
        }
    }
```


### Command-Line Flags  

命令行标志

|Flag|Description|Required|
|----|-----------|--------|
|`--help`, `-h`|Display help <br>显示帮助|No|
|`--input`, `-i`|Path to the glTF or glb file. <br>glTF或glb文件的路径|:white_check_mark: Yes|
|`--output`, `-o`|Output path of the glTF or glb file. Separate resources will be saved to the same directory. <br>glTF或glb文件的输出路径。独立的资源将被保存到同一个目录|No|
|`--binary`, `-b`|Convert the input glTF to glb. <br>将glTF转换为glb|No, default `false`|
|`--json`, `-j`|Convert the input glb to glTF. <br>将glb转换为glTF|No, default `false`|
|`--separate`, `-s`|Write separate buffers, shaders, and textures instead of embedding them in the glTF. <br>编写单独的缓冲区，着色器和纹理，而不是将它们嵌入到glTF中|No, default `false`|
|`--separateTextures`, `-t`|Write out separate textures only. <br>只将纹理单独写出|No, default `false`|
|`--checkTransparency`|Do a more exhaustive check for texture transparency by looking at the alpha channel of each pixel. By default textures are considered to be opaque. <br>通过查看每个像素的Alpha通道，对纹理透明度进行更详尽的检查。默认纹理被认为是不透明的|No, default `false`|
|`--stats`|Print statistics to console for input and output glTF files. <br>将glTF文件的输入和输出统计信息打印到控制台|No, default `false`|
|`--draco.compressMeshes`, `-d`|Compress the meshes using Draco. Adds the KHR_draco_mesh_compression extension.  <br>使用Draco压缩网格。添加KHR_draco_mesh_compression扩展|No, default `false`|
|`--draco.compressionLevel`|Draco compression level [0-10], most is 10, least is 0. <br>Draco压缩等级[0-10]，最大是10，最少是0|No, default `7`|
|`--draco.quantizePositionBits`|Quantization bits for position attribute when using Draco compression. <br>使用Draco压缩时位置属性的量化位|No, default `14`|
|`--draco.quantizeNormalBits`|Quantization bits for normal attribute when using Draco compression.  <br>使用Draco压缩时，法线属性的量化位|No, default `10`|
|`--draco.quantizeTexcoordBits`|Quantization bits for texture coordinate attribute when using Draco compression.  <br>使用Draco压缩时纹理坐标属性的量化位|No, default `12`|
|`--draco.quantizeColorBits`|Quantization bits for color attribute when using Draco compression. <br>使用Draco压缩时颜色属性的量化位|No, default `8`|
|`--draco.quantizeGenericBits`|Quantization bits for skinning attribute (joint indices and joint weights) and custom attributes when using Draco compression. <br>使用Draco压缩时蒙皮属性（关节索引和关节权重）和自定义属性的量化位|No, default `12`|
|`--draco.unifiedQuantization`|Quantize positions of all primitives using the same quantization grid. If not set, quantization is applied separately. <br>使用相同的量化网格对所有基元的位置进行量化。如果未设置，量化将分开应用|No, default `false`|

## Build Instructions  构建说明

Run the tests:  运行测试
```
npm run test
```
To run ESLint on the entire codebase, run:

要在整个代码库上运行ESLint，请运行
```
npm run eslint
```
To run ESLint automatically when a file is saved, run the following and leave it open in a console window:

要在保存文件时自动运行ESLint，请运行以下命令并将其保留在控制台窗口中打开
```
npm run eslint-watch
```

### Running Test Coverage

运行测试覆盖率

Coverage uses [nyc](https://github.com/istanbuljs/nyc).  Run:

覆盖率使用nyc
```
npm run coverage
```
For complete coverage details, open `coverage/lcov-report/index.html`.

有关完整的覆盖详情，请打开

The tests and coverage covers the Node.js module; it does not cover the command-line interface, which is tiny.

测试和覆盖涵盖了Node.js模块;它并不包含很小的命令行界面

## Generating Documentation  生成文档

To generate the documentation:

要生成文档
```
npm run jsdoc
```

The documentation will be placed in the `doc` folder.

文档将被放置在doc文件夹中

## Deploying to npm

部署到npm

* Proofread [CHANGES.md](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/CHANGES.md).
  
  校对CHANGES.MD
* Update the `version` in [package.json](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/package.json) to match the latest version in [CHANGES.md](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/CHANGES.md).
  
  更新package.json中的版本以匹配CHANGES.md中的最新版本
* Make sure to run the tests and ensure they pass.

  确保运行测试并确保它们通过
* If any changes are required, commit and push them to the repository.

  如果需要更改，请提交并将其推送到存储库
* Create and test the package.

  创建并测试包
```
## NPM Pack
## Creates tarball. Verify using 7-zip (or your favorite archiver).
## If you find unexpected/unwanted files, add them to .npmignore, and then run npm pack again.
npm pack

## Test the package
## Copy and install the package in a temporary directory
mkdir temp && cp <tarball> temp/
npm install --production <tarball>
node -e "var test = require('gltf-pipeline');" # No output on success

# If module has executables, then test those now.
```
* Tag and push the release.  标记并推送发布
  * `git tag -a <version> -m "<message>"`
  * `git push origin <version>`
* Publish  发布
```
npm publish
```

Contact [@lilleyse](https://github.com/lilleyse) if you need access to publish.

## Contributions

Pull requests are appreciated!  Please use the same [Contributor License Agreement (CLA)](https://github.com/AnalyticalGraphicsInc/cesium/blob/master/CONTRIBUTING.md) and [Coding Guide](https://github.com/AnalyticalGraphicsInc/cesium/blob/master/Documentation/Contributors/CodingGuide/README.md) used for [Cesium](http://cesiumjs.org/).
