#### Activity生命周期
**生命周期**
>onCreate() -> onStart() -> onResume() ->onPause() -> onStop() -> onDesaroy()

![Activity生命周期图](images/activity_1.gif)

- **启动Activity：**依次调用onCreate() -> onStart() -> onResume(), Activity进入运行状态
- **Activity被覆盖：**调用onPause(), Activity进入暂停状态
- **Activity从被覆盖到恢复到前台：**调用onPause(), Activity进入运行状态