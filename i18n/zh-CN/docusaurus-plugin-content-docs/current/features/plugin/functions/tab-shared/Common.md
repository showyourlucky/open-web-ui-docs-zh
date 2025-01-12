## 共享功能组件

### 阀门和用户阀门 - （可选，但强烈建议使用）

阀门和用户阀门允许用户提供动态信息，如API密钥或配置选项。这些将在给定功能的图形界面菜单中创建可填写字段或布尔开关。

阀门只能由管理员配置，而用户阀门则可以由任何用户配置。

<details>
<summary>示例</summary>

```python
# 定义阀门
    class Valves(BaseModel):
        priority: int = Field(
            default=0, description="过滤操作的优先级"
        )
        test_valve: int = Field(
            default=4, description="控制数值的阀门"
        )
        pass

    # 定义用户阀门
    class UserValves(BaseModel):
        test_user_valve: bool = Field(
            default=False, description="控制真/假（开/关）状态的用户阀门"
        )
        pass

    def __init__(self):
        self.valves = self.Valves()
        pass
```
</details>

### 事件发射器
事件发射器用于在聊天界面中添加额外的信息。与过滤出口类似，事件发射器可以在聊天中追加内容。不同的是，它们不能删除信息。此外，发射器可以在功能执行的任何阶段激活。

有两种不同类型的事件发射器：

#### 状态
这种类型用于在执行步骤时为消息添加状态。这些状态可以在功能运行的任何阶段显示，并且会出现在消息内容的上方。这对于那些延迟LLM响应或处理大量信息的功能非常有用，可以让用户实时了解正在处理的内容。

```python
await __event_emitter__(
            {
                "type": "status", # 设置类型
                "data": {"description": "显示在聊天中的消息", "done": False}, 
                # 注意这里 done 是 False，表示我们仍在发射状态
            }
        )
```

<details>
<summary>示例</summary>

```python
async def test_function(
        self, prompt: str, __user__: dict, __event_emitter__=None
    ) -> str:
        """
        这是一个演示

        :param test: 这是一个测试参数
        """

        await __event_emitter__(
            {
                "type": "status", # 设置类型
                "data": {"description": "显示在聊天中的消息", "done": False}, 
                # 注意这里 done 是 False，表示我们仍在发射状态
            }
        )

        # 执行其他逻辑
        await __event_emitter__(
            {
                "type": "status",
                "data": {"description": "已完成任务的消息", "done": True},
                # 注意这里 done 是 True，表示我们已结束发射状态
            }
        )

        except Exception as e:
            await __event_emitter__(
                {
                    "type": "status",
                    "data": {"description": f"发生错误：{e}", "done": True},
                }
            )

            return f"告诉用户：{e}"
```
</details>

#### 消息
这种类型用于在功能执行的任何阶段向LLM追加消息。这意味着你可以在LLM响应之前、之后或期间追加消息、嵌入图像甚至渲染网页。

```python
await __event_emitter__(
                    {
                        "type": "message", # 设置类型
                        "data": {"content": "这条消息将被追加到聊天中。"},
                        # 注意对于消息类型，我们不需要设置 done 条件
                    }
                )
```

<details>
<summary>示例</summary>

```python
async def test_function(
        self, prompt: str, __user__: dict, __event_emitter__=None
    ) -> str:
        """
        这是一个演示

        :param test: 这是一个测试参数
        """

        await __event_emitter__(
                    {
                        "type": "message", # 设置类型
                        "data": {"content": "这条消息将被追加到聊天中。"},
                        # 注意对于消息类型，我们不需要设置 done 条件
                    }
                )

        except Exception as e:
            await __event_emitter__(
                {
                    "type": "status",
                    "data": {"description": f"发生错误：{e}", "done": True},
                }
            )

            return f"告诉用户：{e}"
```
</details>
