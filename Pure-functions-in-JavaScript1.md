## "pure" 함수는 무엇인가?
순수 함수는 그 범위를 벗어나는 변수의 상태에 의존하지 않고 수정하지 않습니다.  
구체적으로 말하자면, 순수 함수는 동일한 매개 변수가 주어지면 항상 동일한 결과를 반환한다는 의미입니다.  
실행은 시스템의 상태에 의존하지 않습니다.  
순수 함수는 함수형 프로그래밍의 기둥입니다.

예제
````
var values = { a: 1 }

function impureFunction(items) {
  var b = 1

  items.a = items.a * b + 2

  return items.a
}

var c = impureFunction(values)
// 이제 values.a 는 3이고, 불순한 함수는 그것을 수정합니다.
````
여기에서는 주어진 객체의 속성을 수정합니다. 그러므로 우리는 함수의 범위를 벗어나는 객체를 수정합니다 : 함수는 불순합니다.  

````
var values = { a: 1 }

function pureFunction(a) {
  var b = 1

  a = a * b + 2

  return a
}

var c = pureFunction(values.a)
// values.a 는 수정되지 않았습니다. 여전히 1입니다.
````
이제 우리는 단순히 함수의 범위에있는 매개 변수를 수정합니다. 아무것도 외부에서 수정되지 않습니다!  

````
var values = { a: 1 }
var b = 1

function impureFunction(a) {
  a = a * b + 2

  return a
}

var c = impureFunction(values.a)
// 실제로`c`의 값은`b`의 값에 달려 있습니다.
// 더 큰 코드 베이스에서는 결과를 암시 적으로 변화시킬 수 있기 때문에
// 그 점을 잊어 버릴 수도 있습니다.
````
여기서, b는 함수의 범위에 속하지 않는다. 결과는 상황에 따라 달라집니다: 예상한 놀라움!  
````
var values = { a: 1 }
var b = 1

function pureFunction(a, c) {
  a = a * c + 2

  return a
}

var c = pureFunction(values.a, b)
// 여기서 c의 값은 b의 값에 의존한다는 것을 분명히했습니다
// 등 뒤에서 비열한 놀라움은 없었습니다.
````

## 구체적으로 어떻게 보입니까?  
이 코드가 존재한다고 생각하면 :
````
var getMinQuantity = function getMinQuantity(name) {
  // 주어진 이름과 관련하여 숫자를 반환하는 순수한 글꼴.
}
````
람다 프로젝트에 존재할 수있는 다음 코드 예제를 보겠습니다.  

````
var popover = {
  // A bunch of code…

  addQuantityText: function(quantity) {
    var quantityTextOptions = {
      namespace: 'quantity',
      initialChildIndex: 2,
      quantity: quantity,
    }

    try {
      this.formatQuantityText(quantityTextOptions)
    } catch (err) {
      console.log("Couldn't add quantity text!")
    }
  },

  formatQuantityText: function(options) {
    if (!this.$$boxContainer) {
      throw new Error('$$boxContainer is not configured')
    }

    var namespace = options.namespace || 'quantity'
    var quantity = options.quantity || 0
    var initialChildIndex = options.initialChildIndex || 0

    var $$quantity = new Canvas() // implementation details hidden
    $$quantity.name = namespace
    $$quantity.value = quantity
    this.setQuantityTextColor($$quantity)

    this.$$boxContainer.addChild($$quantity, initialChildIndex)

    return $$quantity
  },

  setQuantityTextColor: function($$quantity) {
    if (!$$quantity) return

    var minQuantity = getMinQuantity($$quantity.name)
    var quantity = $$quantity.value || minQuantity
    var hasEnoughQuantity = quantity >= minQuantity

    $$quantity.color = hasEnoughQuantity ? 'green' : 'red'
  },
}
````
모두 불충분 한 addQuantityText(), formatQuantityText() 및 setQuantityTextColor()가 있습니다.  
우리의 맥락에서 addQuantityText()는 $$ boxContainer에 "수량 표시"를 원할 때 실제로 사용되는 메소드입니다.  
이것은 구현 세부 사항을 다루는 진입 점입니다. $$ 수량에 문제가 생길 때 보물 찾기의 시작이 될 가능성이있는 이 방법을 살펴 보겠습니다.  
-하지만 재미있는 것은 아닙니다.  

그것이 쓰여지는 방식은 장기적으로 오류가 날 수 있습니다.  

### 복잡 해지면
이 예제에서는 함수의 실행 순서가 중요합니다.  

그냥 2 줄을 바꾸면 모든 것을 망칠거야. 그것은 명백하게 들릴지 모르지만 디버깅하기가 훨씬 더 어렵습니다.  
````
var popover = {
  // A bunch of code…

  addQuantityText: function(quantity) {
    var quantityTextOptions = {
      namespace: 'quantity',
      initialChildIndex: 2,
      quantity: quantity,
    }

    try {
      this.formatQuantityText(quantityTextOptions)
    } catch (err) {
      console.log("Couldn't add quantity text!")
    }
  },

  formatQuantityText: function(options) {
    if (!this.$$boxContainer) {
      throw new Error('$$boxContainer is not configured')
    }

    var namespace = options.namespace || 'quantity'
    var quantity = options.quantity || 0
    var initialChildIndex = options.initialChildIndex || 0

    var $$quantity = new Canvas() // implementation details hidden
    this.setQuantityTextColor($$quantity)
    $$quantity.name = namespace
    $$quantity.value = quantity

    return $$quantity
  },

  setQuantityTextColor: function($$quantity) {
    if (!$$quantity) return

    var minQuantity = getMinQuantity($$quantity.name)
    var quantity = $$quantity.value || minQuantity
    var hasEnoughQuantity = quantity >= minQuantity

    $$quantity.color = hasEnoughQuantity ? 'green' : 'red'
  },
}
````
이제 코드에 오류가 있습니다. 문제는 감지하기 힘든 일입니다!  
여기에서 setQuantityTextColor()는 $$ 수량의 색상을 담당합니다.  
따라서 객체를 수정하는 마지막 것을 잡아내는 메소드를 탐색 한 다음, 전체 플로우를 다시 구성하여 잘못된 점을 이해해야합니다.  
이 시점에서 formatQuantityText()를 작은 메소드로 분해하여 구현 세부 사항을 단순화 한 것을 후회할 수도 있습니다.  
일반적으로 디버깅 할 때 고려해야 할 많은 코드가 있습니다.  
큰 메소드를 작은 메소드로 분할하는 것이 디버그를 어렵게 만든다면 순수한 함수의 개념이 중요 해지고 있다고 생각하면됩니다.  

### 순수한 함수로 해보자.
코드를 다시 작성하기 위해 최대의 순수 함수를 사용합니다.  
````
var popover = {
  // 시스템 상태의 모든 수정 사항이 여기에 그룹화됩니다.
  // 이 유일한 방법은 디버그를 단순화하고 부작용을 최소화하는 요소 삽입을 담당합니다.  

  addQuantityText: function(quantity) {
    if (!this.$$boxContainer) {
      throw new Error('$$boxContainer is not configured')
    }

    var quantityTextOptions = {
      namespace: 'quantity',
      quantity: quantity,
    }
    var $$quantity = this.formatQuantityText(quantityTextOptions)

    this.$$boxContainer.addChild($$quantity, 2)
  },

  // 이 방법에는 부작용이 없습니다.
  // 그냥 다른 순수 함수를 호출합니다. 원하는대로 캔버스 객체를 생성하고 반환합니다.

  formatQuantityText: function(options) {
    var namespace = options.namespace || 'quantity'
    var quantity = options.quantity || 0

    var $$quantity = new Canvas() // implementation details hidden
    $$quantity.name = namespace
    $$quantity.value = quantity
    $$quantity.color = this.getQuantityTextColor(quantity, namespace)

    return $$quantity
  },

  // 이 방법에는 부작용이 없습니다.
  // 주어진 수량과 관련하여 정확한 색상을 반환합니다.
  getQuantityTextColor: function(quantity, namespace) {
    var minQuantity = getMinQuantity(namespace)
    var hasEnoughQuantity = quantity && quantity >= minQuantity

    return hasEnoughQuantity ? 'green' : 'red'
  },

}
````
이 새로운 버전에는 근본적인 변화가 없습니다. 그러나 무시할 수없는 이점이 있습니다.  

우리가 여기서 한 일은 :
* setQuantityTextColor() 대신 getQuantityTextColor()
* 이 메소드는 이전에 했던 것처럼 객체를 직접 수정하는 대신에 우리가 제공 한 것과 관련된 색상을 반환합니다. 
* 메서드는 변수를 해당 범위 밖으로 간주하지 않습니다.  
* 메서드는 순수한 메서드만 호출합니다.  
* formatQuantityText()에서 $$ 수량 객체의 생성 / 수정을 분리했습니다.  
* addQuantityText()에서 시스템 상태의 수정을 격리했습니다.  

그렇게함으로써 부작용이있는 메소드를 제거했습니다. 코드 유지 관리가 간단해 졌습니다.  
$$ 수량으로 문제가 발생하면 찾을 방법은 하나뿐입니다.  

## 인터페이스 간소화
우리는 여기 공개 방법을 사용했습니다. 비공개로 만들면 완전히 적합 할 것입니다.  

사실, 우리의 역할은 인터페이스를 단순화하는 것이기 때문에 API와 관련이 없습니다.  
Backbone.js를 사용하여 개인적인 방법으로 내 게시물을 살펴보십시오.  

그들이 순수한 것처럼, 특정 상황에 의존하지 않기 때문에 추출하기가 쉽습니다!
````
function getQuantityTextColor(quantity, namespace) {
  var minQuantity = getMinQuantity(namespace)
  var hasEnoughQuantity = quantity && quantity >= minQuantity

  return hasEnoughQuantity ? 'green' : 'red'
}

function formatQuantityText(options) {
  var namespace = options.namespace || 'quantity'
  var quantity = options.quantity || 0

  var $$quantity = new Canvas() // implementation details hidden
  $$quantity.name = namespace
  $$quantity.value = quantity
  $$quantity.color = getQuantityTextColor(quantity, namespace)

  return $$quantity
}
````

그리고 나서 코드에서 나중에 :

````
var popover = {
  // A bunch of code…

  addQuantityText: function(quantity) {
    if (!this.$$boxContainer) {
      throw new Error('$$boxContainer is not configured')
    }

    var quantityTextOptions = {
      namespace: 'quantity',
      quantity: quantity,
    }
    var $$quantity = formatQuantityText(quantityTextOptions)

    this.$$boxContainer.addChild($$quantity, 2)
  },

  // A bunch of code…
}
````

## 순수 함수의 장점
순수 함수의 가장 큰 장점은 부작용이 없다는 것입니다. 해당 범위를 벗어난 시스템 상태는 수정하지 않습니다.  
그런 다음 코드를 단순화하고 명확히합니다. 순수한 함수를 호출 할 때는 다른 곳에서 아무것도 부러뜨리지 않았다는 것을 알기 때문에  
반환 값에 집중해야합니다.  

순수 함수도 강력합니다. 그 실행 순서는 시스템에 아무런 영향을주지 않습니다. 순수 함수로 연산을 병렬화 할 수 있습니다.  

또한 고려해야 할 문맥이 없으므로 순수 함수를 단위 테스트하는 것이 매우 쉽습니다. 그냥 입 / 출력에 집중하십시오.  

마지막으로, 순수한 함수의 사용을 최대화하면 코드가보다 간단하고 유연 해집니다.

## 디자인의 문제
실제로 객체 지향 코드를 수행 할 때 함수형 프로그래밍 개념이 적합하지 않다고 생각할 수 있습니다.  
OOP와 FP는 절대적으로 호환되기 때문에 이것은 잘못된 것입니다!  

실제로 여기에 나온 아이디어는 간단합니다. 시스템에 영향을 미치는 함수의 수를 제한하여 코드를 단순화하십시오.

코드에서 순수한 함수의 수를 최대화하기 위해 두 번 생각하도록 강요하면 유지 보수 / 수명 / 디버그를 더 쉽게 만들 수 있습니다!

글쎄, 당신은 그 생각을 가지고있다. 실제로는 디자인의 문제이며, 예를 들어 get과 set 사이의 선택입니다.