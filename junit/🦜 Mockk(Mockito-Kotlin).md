# ๐ฆ Mockk(Mockito-Kotlin)

์ด๋ฒ์ ์ฝํ๋ง์ผ๋ก ๊ฐ๋ฐํ๋ฉด์ unit test๋ฅผ mockito๋ฅผ ํ์ฉํ์ฌ ์งํํ๊ฒ ๋์๋ค. unit test์ ๊ดํด ์ฐพ์๋ณด๋ ์ค kotlin์์ ์ฌ์ฉํ๋ mockk ๋ผ๋ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ๋ฐ๊ฒฌํ์๋ค. ์ผ๋ฐ์ ์ผ๋ก ๋ง์ด ์ฌ์ฉ๋๋ mockito-core ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ ๊ณ ๋ฏผ ํด๋ณด์์ง๋ง kotlin์ค๋ฝ๊ฒ ๊ฐ๋ฐํ  ์ ์๋ mockk์ ๋ ๋๋ฆฌ๊ฒ ๋์๋ค. ๊ทธ๋ ๊ฒ mockk ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ์ด์ฉํ์ฌ ํ์คํธ๋ฅผ ์งํํ๊ณ  Service Layer ๊ณ์ธต์ ๊ดํด unit test๋ฅผ ํ์๊ณ  ์ด๋ฅผ ์ ๋ฆฌํด๋ณด์๋ค.

### ์์กด์ฑ ์ค์ 

`testImplementation("io.mockk:mockk:1.12.0")`

**์  ๋ฒ์ (1.13.3)์ด ์๋ ๊ตฌ ๋ฒ์  ์ฌ์ฉ ์ด์ **

์  ๋ฒ์  ์ฌ์ฉ ํ ํ์ฌ ์ฌ์ฉ์ค์ธ ์ฝํ๋ฆฐ ๋ฒ์ ๊ณผ ์ถฉ๋ํ์ฌ ๊ตฌ ๋ฒ์  ์ด์ฉ

## Setting

mockk ์ ์ด์ฉํ ๊ฐ์ฒด ์์ฑ์ ์ฌ๋ฌ๊ฐ์ง ๋ฐฉ์์ด ์กด์ฌํ๋ค. ๊ทธ์ค์์๋ Junit5 ๋ฅผ ์ฌ์ฉํ  ์ ํธ๋ฆฌํ๊ฒ ์ฌ์ฉํ  ์ ์๋ ๊ธฐ๋ฅ์ด ์กด์ฌํด์ ํด๋น ๊ธฐ๋ฅ์ ์ฌ์ฉํ๊ธฐ๋ก ๊ฒฐ์ ํ๋ค.

```
@ExtendWith(MockKExtension::class)
internal class PromotionServiceTest {

    @MockK
    private lateinit var promotionRepoMock: PromotionRepo

    @InjectMockKs
    private lateinit var promotionServiceMock: PromotionService
```

์์ ๊ฐ์ด ์ฌ์ฉํ  ์๋น์ค์ ๋ ํฌ์งํ ๋ฆฌ์ ๋ํด์ ์์กด์ฑ์ ๋ถ์ฌํ๊ณ  ์๋ค. ์ฌ์ฉํ ์ด๋ธํ์ด์์ ๋ํ์ฌ ์ ๋ฆฌํ์ฌ ๋ณด์๋ค.

`@ExtendWith(MockKExtendsion::class)`

- ํด๋น ์ด๋ธํ์ด์์ Junit 5์์ ์ ํจํ๋ค.
- ํ๋ผ๋ฏธํฐ์ (MockK, RelaxedMockK)๋ฅผ ์ฌ์ฉํ  ์ ์๊ฒ ํ๋ค.
- ํด๋์ค ์์ฑ๋ค์๋ (MockK, RelaxedMockK, SpyK) ์ด๋ธํ์ด์์ ์ฌ์ฉํ  ์ ์๊ฒ ํ๋ค.

`@Mockk`

- ํด๋น ์ด๋ธํ์ด์์ ์ฌ์ฉํ๊ธฐ ์ํด์๋ MockKAnotations.init()์ด ํธ์ถ ๋์ด์ผ๋๋ค. ํ์ง๋ง Junit5๋ฅผ ์ฌ์ฉํ๊ณ  ์๋ค๋ฉด MockKExtension ์ด๋ธํ์ด์์ ์ฌ์ฉํด์ ์ด๋ฅผ ๋์ ํ  ์ ์๋ค.
- ํ์ฌ ํ์คํธ์ ํ์์์ง๋ง ์์กด์ฑ์ด ๋ถ์ฌ๊ฐ ํ์ํ  ๊ฒฝ์ฐ ํด๋น Mockk ์ด๋ธํ์ด์์ ์ฌ์ฉํด์ ์ด๋ฅผ ํด๊ฒฐํ  ์ ์๋ค. ํด๋น ์ด๋ธํ์ด์์ ๋ถ์ฌํ ๊ฐ์ฒด์์ ํน์  ๊ธฐ๋ฅ์ ํน์  ๋ฆฌํด ๊ฐ์ด ํ์ํ๋ค๋ฉด every๋ฅผ ์ฌ์ฉํด์ ์ค์ ํ  ์ ์๋ค. ํ์ง๋ง ํ์ ์์ ๊ฒฝ์ฐ๋ผ๋ฉด ๊ทธ๋ฅ relaxed ๊ฐ์ true๋ฅผ ๋ฃ์ด์ฃผ๋ฉด ๋๋ค.

`@InjectMockKs`

- ์๋์ผ๋ก @Mock ์ด๋ธํ์ด์์ด ๋ถ์ ๊ฐ์ฒด๋ฅผ ์ฃผ์ ๋ฐ๋๋ค.

## ์ ์ฅ ๊ธฐ๋ฅ test

์ ์ฅ ๊ธฐ๋ฅ์ ํ์คํธํ๊ธฐ ์ํด์ ์๋์ ๊ฐ์ด ๋ก์ง์ ์์ฑํ์๋ค. ํฌ๊ฒ given, when, then์ผ๋ก ๋๋์ด ๋ณด์๋ค.

- given
    - ๋ฐ์ดํฐ ์ ์ฅ ์์ฒญ์ ํ์ํ DTO์ธ PromotionSaveRequest์ ์ ์ฅ ํ ํ์ํ return ๊ฐ์ ์์ฑํ๋ค.
    - every { ํจ์ } returns ๋ฆฌํด ๊ฐ ํ์์ผ๋ก mockingํ ๊ฐ์ฒด์ ํจ์์ ๋ํด ๋ฆฌํด ๊ฐ์ ์ค์ ํ๋ค.
- when
    - Serivce์ save ๊ธฐ๋ฅ์ ์คํํ๋ค.
- then
    - verify ๋ฉ์๋๋ฅผ ํตํด์ save ๋ฉ์๋๊ฐ ์คํ๋์๋์ง ์ฒดํฌํ๋ค. ์ด ๋ ์ธ์๊ฐ์ ๋ฃ๋ ๋ถ๋ถ์ any()๋ฅผ ์ฌ์ฉํ์ฌ์ ์ด๋ค ์ธ์๊ฐ ๋ค์ด์ค๋  ์ฒดํฌํ๋๋ก ํ๋ค. exactly = 1 ์ค์ ์ ํตํด์ ํ๋ฒ ์คํ ๋์๋์ง ํ์ธํ๋ค.
    - assertEquals๋ฅผ ํตํด์ ๋ฆฌํด ๊ฐ์ด ์ฌ๋๋ก ๋ํ๋ฌ๋์ง ํ์ธํ๋ค.
    - id ๊ฐ์ 0์ธ๋ฐ ์ด๋ ์ด๊ธฐ ๊ฐ์ฒด ์ง์  ์์ฑ์ ์์ด๋ ๊ฐ์ ์ค์ ํ  ์ ์์ด์ ๊ธฐ๋ณธ์ผ๋ก 0์ผ๋ก ์ด๊ธฐํ ๋๋๋ก ์ค์  ๋์๋๋ฐ ์ด๋๋ฌธ์ 0์ผ๋ก ์ค์  ๋๊ณ  ์๋ค.

```
@Test
fun save() {
    // given
    val request = PromotionSaveRequest(
        name = "test01",
        endTime = LocalDate.parse("2022-10-10")
    )
    val promotion = Promotion(
        name = "test01",
        endTime = LocalDate.parse("2022-10-10")
    )
every{promotionRepoMock.save(any())}returns promotion

    // when
    val save = promotionServiceMock.save(request)

    // then
verify(exactly = 1){promotionRepoMock.save(any())}
// id ๊ฐ์ ๊ฐ์ฒด๋ฅผ ์์ฑํ  ๋ ์ง์ ํ  ์ ์์์ผ๋ก 0์ด๋  ์ ๋ฐ์ ์๋ค.
    assertEquals("id: 0๊ฐ ์ ์ฅ๋์์ต๋๋ค.", save)
}
```

## ์ญ์  ๊ธฐ๋ฅ test

์ญ์  ๊ธฐ๋ฅ์ mockk์ ์ฌ์ฉํ๋ฉด์ ๋ง์ ์๊ฐ์ ํ๊ฒ๋์๋ค. ์ค์ ๋ก DB์ ์ ์ํด์ ๋ฐ์ดํฐ๋ฅผ ์กฐํ ํ ์ ๊ฑฐํ๋ ๊ฒ์ด์๋๋ผ mocking ํ์ฌ ๋ฐ์ดํฐ๋ฅผ ์ง์ด ๊ฒ ์ฒ๋ผ ํ๋ ๋๋์ด๋ผ ํ์คํธ ํ์ ์ค์  ๋์์ ๋ ๋ค๋ฅผ ์ ์๋ค๊ณ  ๋๊ปด์ก๋ค. 

์ญ์  ๊ธฐ๋ฅ์ ๊ตฌํํ๋ฉด์ ์ด๋ฒ์๋ capture ๋ผ๋ ๊ธฐ๋ฅ์ ์ฌ์ฉํ์๋ค. capture๋ฅผ ์ฌ์ฉํ ์ด์ ๋ ์ญ์  ๋ฉ์๋๊ฐ ์คํ ๋  ๋ ์ญ์ ํ  ๊ฐ์ฒด๊ฐ ์ธ์๋ก ์ ๋๋ก ๋ค์ด๊ฐ๋์ง ํ์ธํ๊ธฐ ์ํจ์ด๋ค.

`capture`

- capture๋ฅผ ์ฌ์ฉํด์ ํจ์์ ์ธ์ ๊ฐ์ด ์ฌ๋๋ก ๋์ด ๊ฐ๋์ง ํ์ธํ  ์ ์๋ค.
- capture๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํด์ capture ํจ์์ ์ธ์ ๊ฐ์ผ๋ก slot ํ์์ ๊ฐ์ฒด๋ฅผ ๋๊ฒจ์ค๋ค. slot์ ์ธ์๋ก ๋์ด๊ฐ ๊ฐ์ฒด๊ฐ ์ ์ฅ๋๊ฒ ๋๊ณ  slot.captured ์์ ์ธ์๊ฐ ๋ค์ด๊ฐ ์๋ค.
- ํ์์ ๋ค์๊ณผ ๊ฐ๋ค. slot = slot<Promotion>() ์ ๋ค๋ฆญ์ ํ๋ผ๋ฏธํฐ์ ํ์์ ์๋ ฅํ๋ฉด ๋๋ค.

```
@Test
fun delete() {
    // given
    val promotion = Promotion(
        name = "test01",
        endTime = LocalDate.parse("2022-10-10")
    )
    val slot = slot<Promotion>()

    // when
every{promotionRepoMock.findById(1L).get()}returns promotion
every{promotionRepoMock.delete(capture(slot))}justruns

    promotionServiceMock.delete(1L)

verify{promotionRepoMock.findById(1L).get()}
verify{promotionRepoMock.delete(any())}

assertEquals(promotion.name, slot.captured.name)
    assertEquals(promotion.endTime, slot.captured.endTime)

}
```

### ๋ง์ฃผํ ์๋ฌ

`io.mockk.MockKException: no answer found for`

์ ์๋ฌ๋ ํธ์ถ ๋์์ ๋ํ ์คํ ์ ์๋ฅผ ํ์ง ์์์ ๋ฐ์ํ ์๋ฌ์ด๋ค.

์ ์๋ฌ๋ฅผ ํด๊ฒฐ ํ๊ธฐ ์ํด์ relaxed mock์ ์ฌ์ฉํ  ์ ์๋ค. 

```bash
// ์์
val mock = mockk<Example>(relaxed = true)
mock.someMethod(1) // --> 0 ๋ฆฌํด
```

์๋ฌ ํด๊ฒฐ์ฑ์ ์๋ ๋ธ๋ก๊ทธ ์ฐธ์กฐ

[https://javacan.tistory.com/entry/kotlin-mock-framework-mockk-intro](https://javacan.tistory.com/entry/kotlin-mock-framework-mockk-intro)

`Only one matching call to PromotionRepo(#1)/save(Any) happened, but arguments are not matching`

```bash
every { promotionRepoMock.save(any()) } returns promotion
```

every๋ฅผ ์ฌ์ฉํ์ฌ ๋ฆฌํด ๊ฐ์ ์ง์ ํ  ๋ ์ธ์ ๊ฐ์ผ๋ก any()๋ฅผ ์ฌ์ฉํ์๋๋ ์์๊ฐ์ ์๋ฌ๋ฅผ ๋ง์ฃผํ์๋ค. any()์ ๊ฒฝ์ฐ ์ถ์ธก์ผ๋ก ์ฌ์ฉ ๋๋ ํจ์์ด๊ณ  eq()๊ฐ ์๋์ผ๋ก ์ฌ์ฉ๋ ๊ฒ์ผ๋ก ๋ณด์ธ๋ค.

[https://mockk.io/#matchers](https://mockk.io/#matchers)

์ ์ฌ์ดํธ๋ฅผ ์ด์ฉํด์ ์ธ์ ๊ฐ์ ํ์๋ง ํ์ธํ๋๋ก ๋ณ๊ฒฝํ์๋ค.

### ์ฐธ์กฐ

[https://github.com/mockk/mockk](https://github.com/mockk/mockk)
