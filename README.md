# NistStandard-research

NIST 后量子标准（FIPS 203 ML-KEM / FIPS 204 ML-DSA）的纯 C 实现，取自 PQ Code Package。

| 目录 | 上游 | 版本 | Commit |
|---|---|---|---|
| `mlkem-native/` | https://github.com/pq-code-package/mlkem-native | v2.1.0 | `4d69d6b0358e678e3a694ea1bb8404a41770f68e` |
| `mldsa-native/` | https://github.com/pq-code-package/mldsa-native | v2.1.0 | `c9999d77599a9f0f0281ad745ad59a83f177b22c` |

## 导入范围

只保留上游 `mlkem/`、`mldsa/` 目录中的可移植 C 代码和 `LICENSE`，源码未做任何修改。已移除：

- `src/native/`：AArch64 / x86_64 / RISC-V / PPC64LE 等算术后端（汇编、intrinsics）
- `src/fips202/native/`：Keccak 原生后端
- `*_native_asm.S`：汇编单编译单元

上游代码中的原生后端由 `MLK_CONFIG_USE_NATIVE_BACKEND_*` / `MLD_CONFIG_USE_NATIVE_BACKEND_*` 控制，默认关闭，所以不需要额外配置。不要开启这些选项，因为对应的文件已经不在本仓库里。

## 构建

调用方需要提供 `randombytes()`（见 `src/randombytes.h`）。

```sh
# ML-KEM：单编译单元构建，参数集为 512 / 768 / 1024
gcc -std=c99 -O2 -Imlkem-native/mlkem -DMLK_CONFIG_PARAMETER_SET=768 \
    -c mlkem-native/mlkem/mlkem_native.c

# ML-DSA：单编译单元构建，参数集为 44 / 65 / 87
gcc -std=c99 -O2 -Imldsa-native/mldsa -DMLD_CONFIG_PARAMETER_SET=65 \
    -c mldsa-native/mldsa/mldsa_native.c
```

也可以把 `src/*.c` 和 `src/fips202/*.c` 一起编译，头文件路径加上 `-I<mlkem|mldsa>`。

用上游 `examples/basic` 的 KAT 测试验证过：ML-KEM-512/768/1024 和 ML-DSA-44/65/87 全部通过，编译器为 gcc (MSYS2 UCRT64)，开启 `-Wall -Wextra -Wpedantic -Wconversion -Werror`。

## 许可证

见各子目录中的 `LICENSE`（Apache-2.0 / ISC / MIT 三选一）。
