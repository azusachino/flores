---
title: JNI技术
created: 2022-08-21 16:00:00
modified: 2023-03-27 10:27:04
aliases: [Java Native Interface]
tags: [CS, Java]
---

### JVM On Load

```cpp
#include <jni.h>
#include <myrica.h>

#include <iostream>

// save global jvm pointer for futher usage
JavaVM *jvm_global_ = nullptr;

// main jni clazz
static const char *jni_clazz_impl_ = "cn/az/code/jni/HelloJni";

// native methods
static JNINativeMethod jni_methods_[] = {
  {(char *)"native_say", (char *)"()V", (void *)jni_native_say}};

/**
* @brief Register several native methods for one class.
*/
static int registerNativeMethods(JNIEnv *env, const char *className, JNINativeMethod *gMethods, int numMethods)
{
  jclass clazz = env->FindClass(className);
  if (clazz == NULL)
  {
    return JNI_FALSE;
  }
  if (env->RegisterNatives(clazz, gMethods, numMethods) < 0)
  {
    return JNI_FALSE;
  }
  return JNI_TRUE;
}

JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *jvm, void* reserved) {

std::cout << "jvm OnLoad" << std::endl;

jvm_global_ = jvm;

// Real Jni Operation Instance
JNIEnv *jni_env = nullptr;
// Get current JniEnv pointer
jint r = jvm_global_->GetEnv(reinterpret_cast<void **>(&jni_env), JNI_VERSION_1_8);
// if fail, return
if (r == JNI_FALSE) {
  std::cerr << "fail to get env" << std::endl;
  return JNI_FALSE;
}

// register native methods
jint ret = registerNativeMethods(jni_env, jni_clazz_impl_, jni_methods_, sizeof(jni_methods_) / sizeof(jni_methods_[0]));
if (JNI_TRUE == ret)
  std::cout << "native methods registered" << std::endl;

// must return valid JNI Version
return JNI_VERSION_1_8;
}
```

### JVM On Unload

```cpp
JNIEXPORT jint JNICALL OnUnload(JavaVM *vm, void *reserved)
{
  std::cout << "jvm OnUnload" << std::endl;
  return JNI_VERSION_1_8;
}
```

### Attach JVM to Thread

```cpp
extern JavaVM *jvm_global_;

int32_t attach_t(JNIEnv **jni_env) {
if (jvm_global_->getEnv(reinterpret<void**>(jni_env), JNI_VERSION_1_8) < 0) {
  if (jvm_global_->AttachCurrentThread(reinterpret<void**>(jni_env), nullptr) < 0) {
    *jni_env = nullptr;
    return -1;
  } else {
    return 0;
  }
}
return -1;
}
```

### `extern "C"` Implementation

```cpp
#include <iostream>

#include "myrica.h"

extern "C" JNIEXPORT void JNICALL jni_native_say(JNIEnv *, jobject)
{
  std::cout << "Hello Jni" << std::endl;
}
```

### Deal with Callback

```cpp
void DemoCallback::onLog(const char* log) {
JNIEnv *jni_env;
attach_t(&jni_env);
if (jni_env == nullptr) {
  perror("fail to attach env");
  return;
}
jclass j_cls = jni_env->FindClass("com/some/som/Clazz");
jstring j_log = jni_env->newStringUTF(log);
jmethod j_method = jni_env->GetMethodID(j_cls, "onLog", "(Ljava/lang/String;)V");

// saved jobject reference
jni_env->CallVoidMethod(this->j_obj, j_method, log);

jni_env->DeleteLocalRef(j_log);
}
```

### 如何加载动态库

- Two approaches to set `java.library.path`
  - VM Args - `java -Djava.library.path=/usr/local/app -jar app.jar`
  - Set System Property - `System.setProperty("java.library.path", "/usr/local/app");`
- Differences
  - 如果想在程序中加载一些库文件，使用第一种方式指定 `java.library.path` 属性时可以正常载入，而使用第二中方式就不行。
  - `java.library.path` 只有在 JVM 启动的时候读取一次，因此在代码中更改 `java.library.path` 是不起任何作用的。
  - 可以在代码中使用 `System.load("/usr/local/app")` 来加载绝对地址指定的本地库。
- `LD_LIBRARY_PATH`
  - JVM 启动时，会使用系统变量 `LD_LIBRARY_PATH` 的值来初始化`java.library.path` 属性。
- `-Djava.library.path` 和   `LD_LIBRARY_PATH` 的区别
  - 使用   `java -Djava.library.path=/usr/local/app`  的方式设置的话，会**覆盖**默认值。
  - 使用 `export  LD_LIBRARY_PATH= /usr/local/app` 的方式设置的话，会**追加**其值到默认值中。

### General Tips

- Minimize marshalling of resources across the JNI layer
- Avoid asynchronous communication between code written in a managed programming language and code written in C++ when possible
- Minimize the number of threads that need to touch or be touched by JNI
- Keep your interface code in a low number of easily identified C++ and Java source locations to facilitate future refactors - 考虑找自动生成 JNI 代码框架

### Threads

- All threads are Linux threads, scheduled by the kernel. They're usually started from managed code (using  `Thread.start()` ), but they can also be created elsewhere and then attached to the  `JavaVM` .
- Until a thread is attached, it has no JNIEnv, and **cannot make JNI calls**.
- **Attaching a natively-created thread causes a  `java.lang.Thread`  object to be constructed and added to the "main"  `ThreadGroup` , making it visible to the debugger. Calling  `AttachCurrentThread()`  on an already-attached thread is a no-op.**
- Threads attached through JNI **must call  `DetachCurrentThread()`  before they exit**.

## References

- [MYRICA - JNI 简单案例](https://github.com/azusachino/myrica)
- [How to set java library path](https://blog.csdn.net/codepython/article/details/42718003)
- [Google JNI Tips](https://developer.android.com/training/articles/perf-jni)
- [JNI 为什么要调用 AttachCurrentThread？](https://keeplooking.top/2020/05/19/Android/AttachCurrentThread)
