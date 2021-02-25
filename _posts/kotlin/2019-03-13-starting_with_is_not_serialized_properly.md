---
title: 'Boolean property name starting with `is` not serialized properly'
layout: post
categories: kotlin
---

아래 테스트는 실패한다.
```kotlin
class SerializeTest {

    @Test
    fun `test_serialized_and_deserialized`() {
        val mapper = jacksonObjectMapper()

        val example = IsBoolExample(true)
        val serialized = mapper.writeValueAsString(example)

        val deserialized = mapper.readValue<IsBoolExample>(serialized)

        assertEquals(example.isTrueOrFalse, deserialized.isTrueOrFalse)
    }
}

data class IsBoolExample(
    val isTrueOrFalse: Boolean
)
```
example은 아래와 같이 is가 빠진 trueOrFalse로 serialize된다.  
isTrueOrFalse가 json 프로퍼티에 없기 때문에 deserialize 시 에러가 발생한다.
```json
{
  "trueOrFalse":true
}
```

## 문제 해결
isTrueOrFalse 프로퍼티에 @get:JsonProperty("isTrueOrFalse")를 추가하면 다음과 같이 serialize되어서 문제를 해결할 수 있다.
```json
{
  "isTrueOrFalse":true
}
```

```kotlin
class SerializeTest {

    @Test
    fun `test_serialized_and_deserialized`() {
        val mapper = jacksonObjectMapper()

        val example = IsBoolExample(true)
        val serialized = mapper.writeValueAsString(example)

        val deserialized = mapper.readValue<IsBoolExample>(serialized)

        assertEquals(example.isTrueOrFalse, deserialized.isTrueOrFalse)
    }
}

data class IsBoolExample(
    @get:JsonProperty("isTrueOrFalse")
    val isTrueOrFalse: Boolean
)
```


## Reference
- <https://github.com/FasterXML/jackson-module-kotlin/issues/80>