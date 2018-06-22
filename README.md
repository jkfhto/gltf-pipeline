# glTF Pipeline

[![License](https://img.shields.io/:license-apache-blue.svg)](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/LICENSE.md)
[![Build Status](https://travis-ci.org/AnalyticalGraphicsInc/gltf-pipeline.svg?branch=master)](https://travis-ci.org/AnalyticalGraphicsInc/gltf-pipeline)
[![Coverage Status](https://coveralls.io/repos/AnalyticalGraphicsInc/gltf-pipeline/badge.svg?branch=master)](https://coveralls.io/r/AnalyticalGraphicsInc/gltf-pipeline?branch=master)

<p align="center">
<a href="https://www.khronos.org/gltf"><img src="doc/gltf.png" onerror="this.src='gltf.png'"/></a>
</p>

Content pipeline tools for optimizing [glTF](https://www.khronos.org/gltf) assets by [Richard Lee](http://leerichard.net/) and the [Cesium team](http://cesiumjs.org/).

Richard Lee和Cesium团队优化glTF文件的内容管道工具

This project is under active development, see the [roadmap](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/issues/1).

该项目正在积极开发中，请参阅路线图

gltf-pipeline can be used as a command-line tool or Node.js module.

gltf-pipeline可以用作命令行工具或Node.js模块

## Instructions  说明

Clone this repo and install [Node.js 6.x or later](http://nodejs.org/).  From the root directory of this repo, run:

克隆此repo并安装Node.js 6.x或更高版本。从此repo的根目录中运行
```
npm install
```

Command-Line Example:  命令行示例
```
node ./bin/gltf-pipeline.js -i ./specs/data/boxTexturedUnoptimized/CesiumTexturedBoxTest.gltf -o output.gltf
```

Compress textures to the dxt1 and etc1 formats  将纹理压缩为dxt1和etc1格式
```
node ./bin/gltf-pipeline.js -i ./specs/data/boxTexturedUnoptimized/CesiumTexturedBoxTest.gltf -o output.gltf --texcomp.dxt1.enable --texcomp.dxt1.quality=10 --texcomp.etc1.enable
```

### Command-Line Flags  命令行标志

|Flag|Description|Required|
|----|-----------|--------|
|`--help`, `-h`|Display help  显示帮助|No|
|`--input`, `-i`|input=PATH, Read unoptimized glTF from the specified file.  <br>input = PATH，从指定文件读取未优化的glTF|:white_check_mark: Yes|
|`--output`, `-o`|output=PATH, Write optimized glTF to the specified file.  <br>输出= PATH，将优化的glTF输出到指定的文件|No|
|`--binary`, `-b`|Write binary glTF file using KHR_binary_glTF extension. <br>使用KHR_binary_glTF扩展编写一个二进制glTF文件|No, default `false`|
|`--separate`, `-s`|Write separate geometry/animation data files, shader files, and textures instead of embedding them in the glTF asset.  <br>编写独立的几何/动画数据文件，着色器文件和纹理，而不是将它们嵌入到glTF资源中|No, default `false`|
|`--separateImage`, `-t`|Write out separate textures, but embeds geometry/animation data files and shader files in the glTF asset.<br>纹理独立，但将几何/动画数据文件和着色器文件嵌入到glTF资源中|No, default `false`|
|`--quantize`, `-q`|Quantize the attributes of this glTF asset using the WEB3D_quantized_attributes extension. <br>使用WEB3D_quantized_attributes扩展来量化此glTF文件的属性|No, default `false`|
|`--encodeNormals`, `-n`|Oct-encode the normals of this glTF asset.  <br>Oct-编码这个glTF文件的法线|No, default `false`|
|`--compressTextureCoordinates`, `-c`|Compress the testure coordinates of this glTF asset.  <br>压缩此glTF文件的纹理坐标|No, default `false`|
|`--removeNormals`, `-r`|Strips off existing normals, allowing them to be regenerated. <br>剥离现有法线，允许它们再生|No, default `false`|
|`--smoothNormals`, `-m`|If normals are missing, they should be generated with smooth normals.  <br>如果法线缺失，则生成平滑法线|No, default `false`|
|`--faceNormals`, `-f`|If normals are missing, they should be generated using the face normal. <br>如果法线缺失，则应使用面法线生成法线|No, default `false`|
|`--cesium`|Optimize the glTF for Cesium by using the sun as a default light source.  <br>使用太阳作为默认光源为Cesium优化glTF文件|No, default `false`|
|`--tangentsBitangents`|If normals and texture coordinates are given, generate tangents and bitangents.  <br>如果有法线和纹理坐标，则生成切线和负切线|No, default `false`|
|`--stats`|Print statistics to console for input and output glTF files.  <br>将输入和输出glTF文件的统计信息打印到控制台|No, default `false`|
|`--kmc.enable`|Materials should be expressed using the KHR_materials_common extension. If other `kmc` flags are enabled, this is implicitly true.  <br>如果启用了其他kmc标志，材料应使用KHR_materials_common扩展表示。|No, default `false`|
|`--kmc.doubleSided`|Declares whether backface culling should be disabled.  <br>声明是否应禁用背面剔除|No, default `false`|
|`--kmc.technique`|The lighting model to use.  要使用的光照模型|No, default `PHONG`|
|`--ao.enable`|Bake ambient occlusion (to vertex data by default). If other `ao` flags are enabled, this is implicitly true. Advanced settings in `lib/bakeAmbientOcclusion.js`  烘烤环境遮挡（默认情况下为顶点数据）。如果启用了其他ao标志，则这是隐含的。 位于lib / bakeAmbientOcclusion.js中的高级设置|No, default `false`|
|`--ao.toTexture`|Bake AO to existing diffuse textures instead of to vertices. Does not modify shaders.  <br>将AO烘焙到现有的漫反射纹理中而不是顶点中。不修改着色器|No, default `false`|
|`--ao.groundPlane`|Simulate a groundplane at the lowest point of the model when baking AO.  <br>烘烤AO时，在模型的最低点模拟地平面|No, default `false`|
|`--ao.ambientShadowContribution`|Amount of AO to show when blending between shader computed lighting and AO. 1.0 is full AO, 0.5 is a 50/50 blend.  <br>在着色器计算光照和AO之间混合时显示的AO量。 1.0表示完全AO，0.5是50/50混合物|No, default `0.5`|
|`--ao.quality`|Quality to use when baking AO. Valid settings are high, medium, and low. <br>烘烤AO时使用的质量。有效的设置是高，中，低|No, default `low`|
|`--texcomp.<format>.enable`|Whether to compress textures with the given compressed texture format. If other `texcomp.<format>` flags are enabled, this is implicitly true. Multiple formats may be supplied by repeating this flag. <format> Must be one of the following: `pvrtc1`, `pvrtc2`, `etc1`, `etc2`, `astc`, `dxt1`, `dxt3`, `dxt5`, `crunch-dxt1`, `crunch-dxt5`. Compressed textures are saved as Cesium and 3D Tiles specific metadata inside `image.extras.compressedImage3DTiles`. More details about texture compression in glTF here: https://github.com/KhronosGroup/glTF/issues/739|No, unless other texcomp options are set.|
|`--texcomp.<format>.quality`|The compressed texture quality from 0 to 10. <br>压缩的纹理质量从0到10|No, default `5`|
|`--texcomp.<format>.bitrate`|The bitrate when using the pvrtc or astc formats. For pvrtc formats this value must be `2.0` or `4.0`. <br>使用pvrtc或astc格式时的比特率。对于pvrtc格式，此值必须为2.0或4.0|No, default `2.0`|
|`--texcomp.<format>.blockSize`|The block size for astc compression. Smaller block sizes result in higher bitrates. This value is ignored if `options.bitrate` is also set. Must be one of the following: `4x4`, `5x4`, `5x5`, `6x5`, `6x6`, `8x5`, `8x6`, `8x8`, `10x5`, `10x6`, `10x8`, `10x10`, `12x10`, `12x12`  <br>astc压缩的块大小。较小的块大小会导致较高的比特率。如果options.bitrate也被设置，这个值将被忽略。必须是以下之一|No, default `8x8`|
|`--texcomp.<format>.alphaBit`|Store a single bit for alpha. Only supported for etc2.  <br>存储一个alpha值。只支持etc2|No, default `false`|

## Build Instructions

Run the tests:
```
npm run test
```
To run ESLint on the entire codebase, run:
```
npm run eslint
```
To run ESLint automatically when a file is saved, run the following and leave it open in a console window:
```
npm run eslint-watch
```

### Building for Cesium integration

Some functionality of gltf-pipeline is used by Cesium as a third party library. The necessary files can be generated using:
```
npm run build-cesium
```

This will output a portion of the gltf-pipeline code into the `dist/cesium` folder, reformatted into AMD style for use with RequireJS and Cesium in the browser.

### Third-party tools

This project bundles several native binaries for texture compression.

* [PVRTexTool](https://community.imgtec.com/developers/powervr/tools/pvrtextool/) - Windows, macOS, and Linux binaries are provided by the PowerVR SDK.
* [etc2comp](https://github.com/google/etc2comp) - Windows, macOS, and Linux binaries built from source.
* [crunch](https://github.com/BinomialLLC/crunch) - Windows binary built from [crunch](https://github.com/BinomialLLC/crunch), macOS binary built from [BKcore/crunch-osx](https://github.com/BKcore/crunch-osx), Linux binary built from [tomerb/linux_fixes](https://github.com/tomerb/crunch/tree/linux_fixes).
* [astc-encoder](https://github.com/ARM-software/astc-encoder) - Windows, macOS, and Linux binaries built from source.

On Linux you may need to install g++ 4.8 as some of the tools link to the libgcc and libstdc++ runtimes.

Run the help command for each tool for a more detailed description of the supported command-line options.

### Building for other integration

Some functionality of gltf-pipeline is used by other projects along with Cesium as a third party library. The necessary files can be generated using:
```
npm run build-cesium-combine
```

This will output a portion of the gltf-pipeline code into the `dist/cesium-combined` folder, reformatted into self-contained file. Currently, only files that have no other local dependencies are allowed.

### Running Test Coverage

Coverage uses [nyc](https://github.com/istanbuljs/nyc).  Run:
```
npm run coverage
```
For complete coverage details, open `coverage/lcov-report/index.html`.

The tests and coverage covers the Node.js module; it does not cover the command-line interface, which is tiny.

## Generating Documentation

To generate the documentation:
```
npm run jsdoc
```

The documentation will be placed in the `doc` folder.

### Debugging

* To debug the tests in Webstorm, open the Gulp tab, right click the `test` task, and click `Debug 'test'`.
* To run a single test, change the test function from `it` to `fit`.

## Deploying to npm

* Proofread [CHANGES.md](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/CHANGES.md).
* Update the `version` in [package.json](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/package.json) to match the latest version in [CHANGES.md](https://github.com/AnalyticalGraphicsInc/gltf-pipeline/blob/master/CHANGES.md).
* Make sure to run the tests and ensure they pass.
* If any changes are required, commit and push them to the repository.
* Create and test the package.
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
* Tag and push the release.
  * `git tag -a <version> -m "<message>"`
  * `git push origin <version>`
* Publish
```
npm publish
```

Contact [@lilleyse](https://github.com/lilleyse) if you need access to publish.

## Contributions

Pull requests are appreciated!  Please use the same [Contributor License Agreement (CLA)](https://github.com/AnalyticalGraphicsInc/cesium/blob/master/CONTRIBUTING.md) and [Coding Guide](https://github.com/AnalyticalGraphicsInc/cesium/blob/master/Documentation/Contributors/CodingGuide/README.md) used for [Cesium](http://cesiumjs.org/).

## Attribution

This product includes components of the PowerVR Tools Software from Imagination Technologies Limited.
https://community.imgtec.com/developers/powervr/powervr-tools-software-eula/

---

<p align="center">
<a href="http://cesiumjs.org/"><img src="doc/cesium.png" onerror="this.src='cesium.png'"/></a>
</p>
