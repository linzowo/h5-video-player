# 视频播放器组件说明文档

使用此播放器可以解决部分播放器无法同时播放 `H264`与`H265`两种编码格式的问题

## 使用方法

1. 下载或拉取仓库数据后文件结构如下

   ```
   │  index.vue			// 实例文件
   │  README.md
   │
   ├─jessibuca				// 播放器源文件
   │      ff.js
   │      ff.wasm
   │      ff.worker.js
   │      index.js
   │      logo.png
   │
   └─jessibucaPlayer		// 播放器组件
           iconfont.woff2
           jessibuca.vue
   ```

2. 将`jessibuca	`文件夹放入`public`文件夹下（老项目如没有`public`放入`static`文件夹）

3. 将`jessibucaPlayer`文件夹放入你的项目组件库(具体位置根据你的项目架构确定)

4. 引入

   1. `/public/index.html`

      ```html
      <head>
      ...
      // 在head中插入
      <script src="/jessibuca/index.js"></script>
      ...
      </head>
      ```

   2. 在需要页面中引入组件，具体实例参考`index.vue`实例文件

## 文档

[官方文档](http://jessibuca.monibuca.com/)

## 特殊事项

在播放器初始化过程中存在一个配置项`decoder: '/jessibuca/index.js',`这个地址和`index.html`中的地址一样是根据你源文件的存放地址来的如果你的存放地址发生了变化请修改这两个地方的地址路径，否则无法运行。

