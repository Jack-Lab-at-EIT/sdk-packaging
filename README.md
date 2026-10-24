# SDK 打包仓库

本仓库只负责构建、自检、打包和发布第三方预编译 SDK，不包含应用程序或 SDK 消费端构建。

## GitHub Actions

在 Actions 页面手动运行对应 workflow：

| Workflow | SDKs | Release tag |
|---|---|---|
| SDK artifacts (VTK) | VTK 9.7.0 with Dawn | `sdk-vtk-9.7.0-webgpu` |
| SDK artifacts (OCCT+Netgen) | OCCT 8.0.1 and Netgen 6.2.2604 | `sdk-occt-netgen-8.0.1-6.2.2604` |
| SDK artifacts (GoogleTest) | GoogleTest 1.18.0 | `sdk-googletest-1.18.0` |

每条 workflow 都会构建并自检三个平台的归档。将 `publish` 设为 `true` 后，只有全部平台任务成功，才会全量替换对应 Release 的资产。每个归档都附带同名 `.sha256` 校验文件。`tools/sdk/releases/` 记录源码固定版本、构建选项和许可证。

## 本地 CMake 入口

为当前平台配置 SDK superbuild：

```sh
cmake -S tools/sdk -B build/sdk -G Ninja
cmake --build build/sdk
```

GitHub Actions workflow 是生产入口。它们先运行 `tools/sdk/selfcheck/` 中对应 SDK 的自检，再用 `tools/sdk/package.cmake` 生成归档。

## 目录

- `.github/workflows/sdk-*.yml`：构建、自检、归档和 Release workflow。
- `tools/sdk/`：固定源码构建描述、制品元数据、自检与发布说明。
- `tools/sdk/platform.cmake`：主机平台到归档 triple 的映射。

消费仓库在各自代码库中维护下载 URL、SHA256 固定值和集成验证。
