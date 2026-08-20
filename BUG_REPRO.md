# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

复用一个已成功使用的幂等键，但把运输参考号改成另一值后，系统没有报告请求冲突，而是返回第一次创建的运输单。请先不要修改代码，定位为什么两个不同请求被视为同一请求，并给出实际证据。 调查全程不要修改目标仓库中的生产代码、测试代码或配置。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/coldchain-custody-task-03
- 仓库地址：https://github.com/zhanglei10281852-gif/coldchain-custody-task-03.git
- parent SHA：c27f44da80edf3aa1d5b7530f2b3e0820f7a117d

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/coldchain-custody-task-03.git bug-repro
cd bug-repro
git checkout --detach c27f44da80edf3aa1d5b7530f2b3e0820f7a117d
go test ./internal/service -run "^TestPlanningRejectsDifferentIdempotencyPayload$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestPlanningRejectsDifferentIdempotencyPayload$" -count=1
--- FAIL: TestPlanningRejectsDifferentIdempotencyPayload (0.50s)
    service_test.go:166: different payload error = <nil>
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/service	0.500s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestPlanningRejectsDifferentIdempotencyPayload$" -count=1
--- FAIL: TestPlanningRejectsDifferentIdempotencyPayload (1.43s)
    service_test.go:166: different payload error = <nil>
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/service	1.650s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

## 通过条件

准确定位根因，指出具体 Go 文件和符号，解释错误行为如何导致题面症状，并给出实际复现、调用链或持久化证据。 完成时目标仓库代码、测试和配置零改动。
