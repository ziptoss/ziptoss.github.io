---
title: '[구글 데이터스튜디오로 일하기 #2] 원하는 데이터 연결하기'
date: '2020-09-09'
---

### 데이터소스 생성

데이터스튜디오는 구글애널리틱스(GA), 스프레드시트 등 구글이 제공하는 솔루션을 비롯해 MySQL 및 여러 외부 솔루션과도 연동이 가능하다는 장점이 있었습니다. 저희는 많은 데이터를 MySQL에서 가져옵니다. 하지만 이따금 외부 솔루션에서 스프레드시트 형태로 데이터를 제공받을 경우가 있었는데, 이럴 때에도 데이터를 바로 데이터스튜디오에 연결할 수 있는 편리함이 있었죠.

![](/image/data_studio02/1.jpeg)

저희가 사용하는 MySQL의 경우, 원하는 쿼리로 데이터를 가져올 수 있다는 것이 또 하나의 큰 장점이었습니다.

![](/image/data_studio02/2.jpeg)

⚠️ 쿼리에 한글이 들어갈 시 데이터스튜디오에서 오류를 반환합니다 (ㅠ.ㅠ) 해결책은 아래에서 소개합니다.

커넥터를 연결하면, 여타 BI툴과 유사하게 필드와 데이터타입(유형)이 자동으로 인식됩니다. 원하는 데이터타입과 다르게 적용된 부분이 있다면 데이터스튜디오에서 제공하는 데이터타입으로 변경해주면 됩니다. 차트를 구현하기 위한 준비는 이것으로 끝입니다!

통계분석 페이지에 그래프를 직접 그리던 이전에는 조금 더 자유도가 높은 차트를 구현할 수 있었지만 그만큼 개발공수가 많이 필요했습니다. 그렇지만 사실 자유도가 높게 그려질수록 직관적인 형태와는 거리가 멀어져가는 (…) 관계가 대체로 성립했기 때문에, 지표의 단순화와 그래프의 분할, 그리고 무엇보다도 업무 효율의 측면에서 모든 통계 데이터를 데이터스튜디오로 이관하기로 결정했습니다.

![](/image/data_studio02/3.jpeg)


### 데이터 혼합

쿼리에서 보던 LEFT JOIN을 데이터스튜디오 내에서도 사용할 수 있습니다.

처음에는 쿼리로 데이터를 조인해서 가져올 수 있는데, 굳이 혼합을 쓸 필요가 있을까 생각했습니다. 쿼리가 더 익숙한 사람들이기에 꽤나 복잡하게 생긴 UI를 가진 이 친구를 최대한 쳐다보지 않으려 했으나, 여러 데이터를 연결하다 보니 필요성이 생기는 순간들이 있었습니다.

![](/image/data_studio02/4.jpeg)

> 저희는 아래와 같은 상황에서 데이터 혼합을 사용했습니다.

#### 1. 다른 커넥터와 연결할 때
가져오는 데이터의 소스가 다른데 이를 연결하고 싶은 상황이 있습니다. 저희는 매물 관리를 전담으로 진행하고 계시는 매물관리팀의 파트를 임시로 구분해서 운영하고 있습니다. 다만 파트 변경이 유동적으로 이루어지다보니, 이를 스프레드시트에서 관리하고 있죠. 그래서 데이터베이스에서 가져온 데이터소스와, 스프레드시트에서 가져온 파트 정보가 담긴 데이터소스를 데이터 혼합 기능으로 묶어서 활용하게 되었습니다.

#### 2. 피봇테이블 값을 필드로 사용할 시
통계분석 페이지를 데이터스튜디오로 이관하며 가장 많이 사용했던 차트 형태가 피봇테이블이었습니다. row, column과 측정항목을 대입하면 순식간에 표가 그려지는 효자였으니까요.

저희는 온오프라인 부동산을 운영하고 있습니다. 고객이 직접 문의를 남기고 → 지점에 방문하여 → 계약이 이루어지는 구조를 가지고 있고, 문의/방문/계약 수가 중요한 지표 중 하나로 활용되고 있습니다. 따라서 지점별 문의/방문/계약 수치를 한 눈에 볼 수 있는 피봇테이블이 필요했습니다. 그리고 계약수 기준 내림차순으로 정렬해야 했죠.

type이라는 필드에 문의/방문/계약을 값으로 가지고 있도록 쿼리를 날렸고, 피봇테이블에서는 문의/방문/계약이 column으로 찰떡같이 달라붙기를 기대했습니다. 원하는 형태는 맞았으나, column에 들어가 있는 각각의 지표들(문의/방문/계약)을 각각의 필드로 인식하지 못하더군요. 특정 지표를 기준으로 정렬 순서를 설정할 수 없었습니다.

그래서 데이터 혼합을 사용했습니다. 문의/방문/계약을 각각의 데이터소스로 만들고 혼합시키면, 각각의 값을 측정항목으로 활용할 수 있었습니다.

### 데이터 추출

편리한 기능을 제공하는 데이터스튜디오지만, 쿼리가 복잡하거나 가져올 데이터 양이 많을 경우 보고서의 로딩속도가 느려진다는 답답함이 있었습니다. 그래서 발견하게 된 기능이 바로 ‘데이터 추출’ 입니다.

데이터 추출 기능은 데이터가 매일 업데이트되는 시간을 정해둠으로써, 데이터 로딩 속도를 크게 개선할 수 있는 방법입니다. 데이터 추출은 미리 생성된 데이터소스를 기반으로 하기 때문에, 필요한 데이터가 있다면 미리 데이터소스를 생성해두어야 합니다.

데이터 소스를 생성할 때와 마찬가지로, 커넥터 중에서 ‘데이터 추출’을 선택하면 됩니다.

![](/image/data_studio02/5.png)

연결해둔 데이터소스를 선택한 후에, 가져올 필드 및 업데이트 주기를 설정하면 데이터소스와 똑같이 사용할 수 있습니다.

### 한글 호환 문제 

이렇게 강력한 기능을 무료로 제공하는 데이터스튜디오지만, 한글 사용에 있어서 몇가지 치명적인 어려움이 있었습니다.

#### 1. 쿼리에 한글이 들어갈 시

데이터스튜디오는 쿼리에 한글이 포함되었을 때에 정상적으로 그래프를 뽑아주지 못했습니다.
그래서 이를 해결하기 위한 꼼수를 생각해내야 했습니다.

```sql
SELECT * FROM contracts WHERE type = '전세'
SELECT * FROM contracts WHERE type =
    (SELECT words FROM datastudio WHERE id = 1)
```

임시 테이블을 생성하고, 서브쿼리를 활용해 한글을 가져와주는 거죠.😇
복잡한 쿼리를 날릴 때에는 가독성이 떨어진다는 단점이 있지만, 그렇지 않을 때에는 큰 불만 없이 사용하고 있습니다.
(무료니까요)

#### 2. 필터에 한글 포함 시

이렇게 열심히 한글 값을 가져왔는데, 한글이 드롭다운이나 필터에 들어가면 또 오류가 발생합니다.
이럴 때 활용할 수 있는 방법이 앞서 소개한 ‘데이터 추출’입니다.
데이터소스 A 생성 → A를 데이터추출해서 A’ 생성 → A’을 보고서에 활용하면 드롭다운도, 필터도 한글로 활용할 수 있습니다!