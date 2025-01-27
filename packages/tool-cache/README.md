# `@actions/tool-cache`

> Functions necessary for downloading and caching tools.

## Usage

#### Download

You can use this to download tools (or other files) from a download URL:

```js
const tc = require('@actions/tool-cache');

const node12Path = await tc.downloadTool('https://nodejs.org/dist/v12.7.0/node-v12.7.0-linux-x64.tar.gz');
```

#### Extract

These can then be extracted in platform specific ways:

```js
const tc = require('@actions/tool-cache');

if (process.platform === 'win32') {
  const node12Path = await tc.downloadTool('https://nodejs.org/dist/v12.7.0/node-v12.7.0-win-x64.zip');
  const node12ExtractedFolder = await tc.extractZip(node12Path, 'path/to/extract/to');

  // Or alternately
  const node12Path = await tc.downloadTool('https://nodejs.org/dist/v12.7.0/node-v12.7.0-win-x64.7z');
  const node12ExtractedFolder = await tc.extract7z(node12Path, 'path/to/extract/to');
}
else if (process.platform === 'darwin') {
  const node12Path = await tc.downloadTool('https://nodejs.org/dist/v12.7.0/node-v12.7.0.pkg');
  const node12ExtractedFolder = await tc.extractXar(node12Path, 'path/to/extract/to');
}
else {
  const node12Path = await tc.downloadTool('https://nodejs.org/dist/v12.7.0/node-v12.7.0-linux-x64.tar.gz');
  const node12ExtractedFolder = await tc.extractTar(node12Path, 'path/to/extract/to');
}
```

the Archive helper can be used as a shortcut for both download and extraction of an archive:

 ```js
 const {Archive} = require('@actions/tool-cache');

 const node12Archive = await Archive.retrieve('https://nodejs.org/dist/v12.7.0/node-v12.7.0-linux-x64.tar.gz')
 const node12ExtractedFolder = await node12Archive.extract('path/to/extract/to')
 ```

#### Cache

Finally, you can cache these directories in our tool-cache. This is useful if you want to switch back and forth between versions of a tool, or save a tool between runs for self-hosted runners.

You'll often want to add it to the path as part of this step:

```js
const tc = require('@actions/tool-cache');
const core = require('@actions/core');

const node12Path = await tc.downloadTool('https://nodejs.org/dist/v12.7.0/node-v12.7.0-linux-x64.tar.gz');
const node12ExtractedFolder = await tc.extractTar(node12Path, 'path/to/extract/to');

const cachedPath = await tc.cacheDir(node12ExtractedFolder, 'node', '12.7.0');
core.addPath(cachedPath);
```

You can also cache files for reuse.

```js
const tc = require('@actions/tool-cache');

const cachedPath = await tc.cacheFile('path/to/exe', 'destFileName.exe', 'myExeName', '1.1.0');
```

#### Find

Finally, you can find directories and files you've previously cached:

```js
const tc = require('@actions/tool-cache');
const core = require('@actions/core');

const nodeDirectory = tc.find('node', '12.x', 'x64');
core.addPath(nodeDirectory);
```

You can even find all cached versions of a tool:

```js
const tc = require('@actions/tool-cache');

const allNodeVersions = tc.findAllVersions('node');
console.log(`Versions of node available: ${allNodeVersions}`);
```
