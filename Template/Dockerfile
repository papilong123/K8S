# 使用多阶段构建，这里命名一个builder阶段，生成编译后的app
FROM base_image:tag AS builder

ARG arg_key=[default_value1]     # 声明变量
ENV env_key=value2     # 声明环境变量

# 构建整体的目录结构，build时依赖的文件和工具包等
COPY src dst
RUN command1 && command2 ...

WORKDIR /path/to/work/dir   # 设置工作目录

# 构建编译环境
COPY src dst
RUN command3 && command4 ...

# 编译生成entry.app
COPY src dst
RUN compile_entry_app

# 构建最终镜像的阶段，只保留应用和其运行环境，编译的依赖都不需要
FROM base_image:tag
COPY src dest    # 复制运行环境
WORKDIR /path/to/work/dir   # 设置工作目录
COPY --from=builder entry.app . # 从builder阶段复制app
# 容器入口
ENTRYPOINT ["/entry.app"]  # 指定容器启动时默认执行的命令
CMD ["--options"] # 指定容器启动时默认命令的默认参数


ref:
https://mp.weixin.qq.com/s/hPalvdjs0zRkqetq_dOR8A
