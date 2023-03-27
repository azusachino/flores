---
title: trick 2022
created: 2022-09-19 08:00:00
modified: 2023-03-27 10:22:15
tags: [Collection, Trick]
---

## 解决 Win11 下 Desktop Window Manager GPU 占用过高问题

Settings -> Display -> Graphics -> Change default graphics settings

关闭 Hardware-accelerated GPU scheduling 选项

## 观察 Chrome 同步状态

强制同步标签页的基本方法: 新增、删除一个标签页

```sh
# 可以看到 chrome 相关状态
chrome://sync-internals/
```

## MacOS Syncthing 使用

使用 syncthing 同步时，自动创建的文件夹，主目录的权限针对当前用户是可读、可写的，而里面的子目录都是近可读的，就会产生十分神奇的现象。

手动调整权限，并向下覆盖即可。

## Install GraalVM on Macos

```sh
# install local files
gu -L install local_abc.jar
```

1. Navigate to [GraalVM Releases repository on GitHub](https://github.com/graalvm/graalvm-ce-builds/releases). Select Java 11 based or Java 17 based distribution for macOS, and download.
2. Unzip the archive.

    ```shell
    tar -xzf graalvm-ce-java<version>-darwin-amd64-<version>.tar.gz
    ```

    Alternatively, open the file in Finder.

    > Note: If you are using macOS Catalina and later you may need to remove the quarantine attribute:
    >
    > ```shell
    >  sudo xattr -r -d com.apple.quarantine /path/to/graalvm
    > ```

3. Move the downloaded package to its proper location, the `/Library/Java/JavaVirtualMachines` directory. Since this is a system directory, `sudo` is required:

    ```shell
      sudo mv graalvm-ce-java<version>-<version> /Library/Java/JavaVirtualMachines
    ```

    To verify if the move is successful and to get a list of all installed JDKs, run `/usr/libexec/java_home -V`.

4. There can be multiple JDKs installed on the machine. The next step is to configure the runtime environment:

    - Point the `PATH` environment variable to the GraalVM `bin` directory:

      ```shell
       export PATH=/Library/Java/JavaVirtualMachines/<graalvm>/Contents/Home/bin:$PATH
      ```

    - Set the `JAVA_HOME` environment variable to resolve to the GraalVM installation directory:

      ```shell
       export JAVA_HOME=/Library/Java/JavaVirtualMachines/<graalvm>/Contents/Home
      ```

5. To check whether the installation was successful, run the `java -version` command.

### SSH-KEYGEN

```sh
ssh-keygen -t rsa -b 4096 -C your_email.com
```

### 突破 Chrome 证书无效限制

**在 Chrome 的错误页面**，点页面空白处一下让键盘焦点处于页面内，然后**依次按下 thisisunsafe**，即可忽略证书错误

## References

- [Force Chrome to sync bookmarks](https://superuser.com/questions/129410/force-chrome-to-sync-bookmarks)
