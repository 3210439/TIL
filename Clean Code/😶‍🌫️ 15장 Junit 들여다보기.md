# 😶‍🌫️ 15장 Junit 들여다보기

## JUnit프레임워크

- 시작은 켄트 벡과 에릭 감마 두사람이 시작했다.
- 의도를 명확히 표현하려면 조건문을 캡슐화해야 한다. 즉 조건문을 메서드로 뽑아내 적절한 이름을 붙인다.
    
    ```jsx
    public String compact(String message) {
    	if(shouldNotCompact())
    		return Assert.format(message, expected, actual);
    		
    	findCommonPrefix();
    	findCommonSuffix();
    	String expected = compactString(this.expected);
    	String actual = compactString(this.actual);
    	return Assert.format(message, expected, actual);
    }
    
    private boolean shouldNotCompact() {
    	return expected == null || actual == null || areStringEqual();
    }
    ```
    
- 부정문은 긍정문보다 이해하기 어렵다.
    - shouldNotCompact() 메소드를 긍정문으로 변경한다.
    
    ```jsx
    private boolean canBeCompacted() {
    	return expected != null && actual != null && !areStringsEquals();
    }
    ```
    
- 위 compact라는 함수는 압축한다고 말하지만 실제로는 조건문이 false가 되면 압축하지 않는다. 그래서 formatCompatedComparison이라는 이름이 적합하다. 새 이름에 인수를 고려하면 가독성이 훨씬 더 좋아진다.

```jsx
public String formatCompactedComparison(String message)
```