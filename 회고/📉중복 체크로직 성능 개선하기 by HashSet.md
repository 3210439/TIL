# 📉중복 체크로직 성능 개선하기 by HashSet

```
val transactionKeyList = paymentCancelRepo.findAllByPayment(payment).map{
    it.transactionKey
}

paymentDto.cancels?.forEach{cancel->
var isNew = true
    transactionKeyList.forEach{transactionKey->
if(cancel.transactionKey == transactionKey) isNew = false
}
if(isNew){
        val paymentCancel = paymentCancelMapper.toEntity(cancel, payment)
        paymentCancelRepo.save(paymentCancel)
    }
}
```

나는 위와같이 코드를 작성하였다. 코드를 설명하기 전에 상황을 설명하자면 이렇다.
토스페이먼츠를 통해 결제 취소 로직을 작성하고 있었다.

토스는 결제 관련 api를 호출하면 반환값으로 payment를 반환하는데 부분 취소를 여러번 하면 cancels 값 안에 여러개의 cancel 데이터가 들어가있다. 그중 나는 새로운 cancel 값을 db에 저장하려고 한다. 하지만 해당 데이터는 이전에 저장한 cancel 데이터가 있을 수 있음으로 중복 체크가 필요하다. 

그래서 처음에 생각해낸 방법은 위와같이 foreach문을 2번 돌리는 방법이다. response 로 받은 cancels값을 일일이 db에 저장해둔 모든 값과 비교해보는 것이다. 하지만 이런 방식으로 중복 체크를 할 경우 **O(n^2)의** 시간 복잡도가 나오게 되어 데이터가 많아질 때 성능 저하가 발생할 수 있다.

이러한 상황의 경우 해결책으로 hashSet을 활용할 수 있다. 데이터를 비교할 때 hash로 접근한다는 점 덕분에 **O(n^2)시간 복잡도에서 O(n) 시간 복잡도로 시간 복잡도를 낮출 수 있다는 장점이 있다.**

아래는 개선된 코드이다.

```
val transactionKeySet = paymentCancelRepo.findAllByPayment(payment).map{
    it.transactionKey
}.toHashSet()

paymentDto.cancels?.filter{cancel->!transactionKeySet.contains(cancel.transactionKey)}
?.forEach{cancel->
val paymentCancel = paymentCancelMapper.toEntity(cancel, payment)
    paymentCancelRepo.save(paymentCancel)
}
```

기존에 list로 생성하던 transactionKeyList 부분을 transactionKeySet으로 변경하였고 kotlin의 filter 기능을 통해서 데이터베이스에 저장되어 있지 않은 cancel들을 n의 시간 복잡도로 찾아내었다. 그리고 저장되어 있지 않던 cancel에 대해서만 데이터베이스에 insert 하도록 했다.

위와같이 중복체크를 할 때 hashSet을 사용해서 성능을 개선하는 방법을 알아보았다.