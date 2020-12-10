##  Promise 的逻辑方法



Promise 对象有几个组合方法，可以将多个承诺合并成一个进行处理

分别是 Promise.all, Promise.race, Promise.allSettled, Promise.any

这些方法都可以接收一组承诺，返回一个新的承诺

```javascript
Promise.all(values)
```

其中参数 values 是一个可迭代对象，比如数组

在后文中使用词语“成功”表示承诺 resolve，“失败”表示承诺 reject

### **Promise.all**

Promise.all 方法返回的承诺会等到参数中所有的承诺都成功之后才会成功，只要其中有一个失败了则返回的承诺也会立即失败，不会等到那些还挂起的承诺有结果

![img](http://my.flytodream.cn/docImgage/54543534.jpg)

Promise.all 方法可以用来处理那些缺一不可的逻辑

示例：同时发出多个请求都成功后才能进行下一步

```javascript
const coffee = fetch('/coffee')
const tea = fetch('/tea')
const me = fetch('/me')

// 我全都要
const res = await Promise.all([coffee, tea, me])
```

### **Promise.race**

Promise.race 方法返回参数中最快的那个承诺，如果最快的那个承诺成功则返回的承诺也会成功，否则就是失败，不会等到那些还挂起的承诺有结果

![img](http://my.flytodream.cn/docImgage/5345789347583458.jpg)

示例：给一个复杂任务设定一个超时时间

```javascript
// 设置一个定时器，时间到了就 reject 一个承诺
const timeout = new Promise((resolve, reject) => {
  setTimeout(reject, 3000)
})

const missions = fetch('/missions')

try {
  const res = await Promise.race([timeout, missions])
  // missions 任务成功
} catch () {
  // 时间超过 3 秒了或者任务失败了
}
```

### **Promise.allSettled**

Promise.allSettled 方法返回的承诺对象会等到参数中所有的承诺对象都完成后才成功，无论怎样该方法返回的承诺都不会失败

![img](http://my.flytodream.cn/docImgage/83222342.jpg)



**和 Promise.all 方法的区别**

Promise.all 方法需要参数中的所有承诺都成功

而 Promise.allSettled 对参数中的承诺是成功还是失败并不关心，只要有结果就行

示例：一次性上传多个文件，其中上传成功和上传失败的互不影响，在一轮上传任务完成之后，可以筛选出那些上传失败的重新上传。

```javascript
const upload = file => {
  const formData = new FormData()
  formData.append('file', file)
  return fetch('/upload', {
    method: 'POST',
    body: formData
  })
}

document.querySelector('input[type="file"]').addEventListener('change', function(e) {
  if (!e.target.value) return
  const files = e.target.files
  const promises = files.map(file => upload(file))

  const res = await Promise.allSettled(promises)
  // 全部上传任务都完成了，找出上传失败的重新上传
})
```

该方法是 ES2020 新添加的方法

### **Promise.any**

Promise.any 方法返回一组承诺中最快成功的那个承诺，如果参数中所有承诺都失败了，那么返回的承诺也失败

![img](http://my.flytodream.cn/docImgage/4fsdfd.jpg)

**和 Promise.race 方法的区别**

Promise.race 返回参数中最快的那个承诺，无论它是成功还是失败

而 Promise.any 关注的是参数中最快同时还必须成功的那个承诺

和 Promise.all 方法的区别

Promise.any 和 Promise.all 是完全相反的

Promise.any 参数中全部承诺都失败了才会失败，Promise.all 参数中全部承诺都成功了才会成功

Promise.any 参数中一旦有一个承诺成功了返回的新承诺就会成功，Promise.all 参数中一旦有一个承诺失败了返回的新承诺就会失败

示例：同时加载一组图片，但是我们只需要用到其中的一张，就可以用 Promise.any 方法挑选出最先加载成功的那张图片

```javascript
const fetchImg = async (url) => {
  return fetch(url).then(res => {
    if (!res.ok) {
      throw new Error('HTTP error!')
    } else {
      return res.blob()
    }
  })
}

cosnt img1 = fetchImg('/1.png')
const img2 = fetchImg('/2.png')

try {
  const res = await Promise.any([img1, img2])
  const url = URL.createObjectURL(res)
  const img = document.createElement('img')
  img.src = url
  document.body.appendChild(img)
} catch () {
  // 一个都没加载成功 QAQ
}
```

该方法还处于草案中，目前最新的 Chrome, Firefox, Safari 支持。