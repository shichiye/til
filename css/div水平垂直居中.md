# div水平垂直居中
## 定宽高
### 使用定位+margin
```js
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -25px;
  margin-top: -25px;
  width: 50px;
  height: 50px;
  background: yellow;
```

### 使用定位+transform
```js
  position: absolute;
  left: 50%;
  top: 50%;
  width: 50px;
  height: 50px;
  background: yellow;
  transform: translate(-50%, -50%);
```
### flex布局
```js
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100px;
  height: 100px;
  background: yellow;
```

## 不定宽高
```js
  position: absolute;
  left: 50%;
  top: 50%;
  background: yellow;
  z-index: 1;
  transform: translate(-50%,-50%);
```
