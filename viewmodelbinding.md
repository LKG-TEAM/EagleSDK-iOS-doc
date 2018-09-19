[viewmodel_bind]:./assets/viewmodel_s1.png

# ViewModel与Controller的绑定


- 绑定优先级
bindMap.plist > controller.json > code inline, *只有一个viewmodel通过code inline方式绑定时会自动绑定，有多个时会抛出一个异常。*

- 绑定流程
![viewmodel_bind]