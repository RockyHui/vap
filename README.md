# VAP支持跳帧播放 

## 背景

VAP是腾讯开源的一款跨平台的动画播放方案，拥有优秀的性能，可实现复杂的动画效果。具体功能特色可以查看官方介绍：[github地址](https://github.com/Tencent/vap)

————————————————————————————

查看vap源码接口发现，目前只支持一个动画从头到位完整的播放，如果我们想在播放过程中去控制跳到任意一帧，这种场景目前是不支持的。因此对这个播放框架进行了底层逻辑的调整，使得可以支持跳帧播放。

## 用法
### 1.播放本地mp4格式的动画资源

调用动画播放的方法和之前使用方式一样，只不过多了一个`canSeek`的参数，表示是否支持跳帧操作。

```
let path = Bundle.main.path(forResource: "animation", ofType: "mp4")
loadingAnimationView.playHWDMP4(path, repeatCount: 0, canSeek: true, delegate: self)
```

### 2.在代理方法中实现跳帧
```
func viewFrameConvert(_ frameIndex: Int, view container: UIView!) -> Int {
        if frameIndex == 30 { //播放到第30帧，跳转到第10帧循环播放
        	// ..........
        	return 10
        }
        return frameIndex
    }
```
调整后的代码多增加了一个代理方法`viewFrameConvert(frameIndex: container: )`，用于控制跳帧行为。

当每次播放下一帧的时候，都会调用该方法，询问是否继续播放当前帧还是跳到指定帧，如果不需要跳帧操作，就返回当前的`frameIndex`即可；需要跳帧，则返回指定的帧序列号。

因此便是实现了调整播放和循环播放动画中某一段动画的效果。