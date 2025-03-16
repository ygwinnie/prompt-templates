# Coze代码节点撰写指南

## 基本框架

Coze代码节点使用Python语言，遵循以下基本框架：

```python
async def main(args):
    # 从args中获取参数
    params = args.params
    
    # 处理业务逻辑
    
    # 返回结果
    return result
```

## 输入参数处理

1. **安全获取参数**：使用`get`方法而非直接索引
   ```python
   # 推荐
   stu_info = params.get("stu_info", {})
   
   # 不推荐
   stu_info = params["stu_info"]  # 如果键不存在会抛出KeyError
   ```

2. **设置适当的默认值**：根据参数预期类型设置默认值
   - 对象/字典：`params.get("user_info", {})`
   - 数组/列表：`params.get("items", [])`
   - 字符串：`params.get("name", "")`
   - 数值：`params.get("count", 0)`
   - 布尔值：`params.get("enabled", False)`

3. **嵌套数据的安全获取**：对嵌套数据也使用`get`方法
   ```python
   # 如果data可能不存在或内部user键可能不存在
   username = params.get("data", {}).get("user", {}).get("name", "")
   ```

## 数据类型处理

1. **保持数据类型一致性**：输入和输出的数据类型应符合API预期
   ```python
   # 如果API预期fields是对象
   output_record = {
       "fields": fields_content,  # 直接使用JSON对象
       "record_id": record_id
   }
   
   # 如果API预期fields是JSON字符串
   import json
   output_record = {
       "fields": json.dumps(fields_content, ensure_ascii=False),
       "record_id": record_id
   }
   ```

2. **处理列表数据**：遍历列表时考虑边界情况
   ```python
   # 安全地从列表获取元素
   record_id = outputList[0].get('record_id') if len(outputList) > 0 else "default_id"
   ```

## 错误处理

1. **使用try-except捕获可能的异常**
   ```python
   try:
       result = process_data(params)
       return result
   except Exception as e:
       return {"error": str(e), "success": False}
   ```

2. **验证输入数据**：在处理前验证关键参数
   ```python
   if not params.get("required_field"):
       return {"error": "必需的字段缺失", "success": False}
   ```

## 输出格式

1. **遵循预期的输出格式**：确保返回的数据结构符合后续节点的预期
   ```python
   # 返回数组格式
   return [item1, item2, item3]
   
   # 返回对象格式
   return {"key1": value1, "key2": value2}
   ```

2. **保持字段命名一致性**：输出字段命名应与API文档一致

## 性能优化

1. **避免不必要的循环**：特别是嵌套循环可能导致性能问题
2. **使用适当的数据结构**：根据操作选择合适的数据结构（字典、列表等）
3. **减少数据转换**：尽量减少不必要的数据类型转换操作

## 代码可读性

1. **添加适当注释**：解释复杂逻辑或关键步骤
2. **使用有意义的变量名**：变量名应清晰表达其用途
3. **结构化代码**：将复杂逻辑分解为小函数
   ```python
   async def main(args):
       params = args.params
       processed_data = process_input(params)
       result = format_output(processed_data)
       return result
       
   def process_input(params):
       # 处理输入逻辑
       return processed_data
       
   def format_output(data):
       # 格式化输出逻辑
       return formatted_data
   ```

## 调试技巧

1. **使用日志**：如果Coze支持日志功能，可记录关键步骤
2. **增量开发**：先确保基本功能正常，再添加高级功能
3. **使用测试数据**：准备各种边界情况的测试数据

## 常见陷阱

1. **忽略边界情况**：如空输入、错误格式等
2. **硬编码值**：避免在代码中硬编码容易变化的值
3. **类型错误**：JavaScript和Python类型系统差异导致的问题
4. **未处理异步操作**：确保正确处理异步函数和Promise

遵循以上指南，可以编写出更加健壮、可维护的Coze代码节点。