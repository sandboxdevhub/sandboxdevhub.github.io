---
title: Apex LeadConvert Class의 모든 것
author: Dynamo J
date: 2024-07-25 23:34:00 +0900
categories: [Salesforce, Apex]
tags: [apex]
---

Salesforce에서 리드는 가장 중요한 오브젝트 중 하나입니다. 제품/서비스를 구매하는 데 관심이 있는 잠재 고객의 정보를 리드 레코드에 기록합니다. Salesforce의 리드 Convert까지의 단계를 기록하는 것은 리드의 진행 상황을 추적하고 가장 중요한 기회를 식별할 수 있도록 하기 때문에 Sales 에서 중요한 단계입니다. Lead Convert 기능을 사용하면 Lead에 담아둔 정보를 바탕으로 빠르게 Account, Contact, Opportunity 레코드로 전환할 수 있습니다. 하지만 고객이 커스텀된 리드 컨버트 기능이 필요하다면 스탠다드 기능이 아닌, Apex를 사용해서 리드를 전환하도록 구현해야합니다.

제가 LeadConvert 기능을 사용하면서 공부한 내용을 공유합니다 :)

## 리드 전환이란?

Salesforce에서 리드는 제품이나 서비스에 관심을 보였지만 아직 고객이 아닌 개인이나 회사, 즉 잠재고객입니다.

세일즈포스에서의 리드 전환은 리드 레코드에서 새 계정, 연락처 및 기회 레코드를 만들어 리드를 고객으로 전환하는 프로세스입니다. 리드를 전환하는 과정에서는 'Lead Convert' 기능만의 고유한 속성들이 있습니다. 특정 필드들의 업데이트는 'Lead Convert' 기능을 통해서만 업데이트 가능합니다. 기본적으로 제공하는 Convert 기능을 사용하는 것 외에 비즈니스에 맞춘 Lead Convert가 필요하다면 convertLead() 클래스를 사용해야합니다.

convertLead는 Database.LeadConvert 클래스의 인스턴스를 매개변수로 사용합니다.

## Database.convertLead 예시

```java
// 새 리드 객체를 생성하고 LastName과 Company 필드를 설정합니다.
Lead myLead = new Lead(LastName = 'Fry', Company='Fry And Sons');

// 생성된 리드를 데이터베이스에 삽입합니다.
insert myLead;

// 새로운 Database.LeadConvert 객체를 생성합니다.
Database.LeadConvert lc = new Database.LeadConvert();

// 변환할 리드의 ID를 설정합니다.
lc.setLeadId(myLead.id);

// 변환된 상태를 나타내는 LeadStatus 레코드를 조회합니다.
LeadStatus convertStatus = [SELECT Id, ApiName FROM LeadStatus WHERE IsConverted=true LIMIT 1];

// 변환된 상태를 LeadConvert 객체에 설정합니다.
lc.setConvertedStatus(convertStatus.ApiName);

// 리드를 변환하고 결과를 LeadConvertResult 객체에 저장합니다.
Database.LeadConvertResult lcr = Database.convertLead(lc);

// 변환이 성공했는지 확인합니다.
System.assert(lcr.isSuccess());
```

## Convert Lead의 주요 특징

#### 1\. Lead record의 details를 바탕으로 Account, Contact, Opportunity 레코드 생성

리드를 전환하면 리드의 상세 정보를 바탕으로 새로운 계정(Account), 연락처(Contact), 그리고 선택적으로 기회(Opportunity) 레코드가 생성됩니다. 이는 영업 프로세스를 구조화하고 관리하는 데 매우 유용합니다.

#### 2\. 기회 레코드 생성은 선택 사항

리드 전환 시 반드시 기회(Opportunity) 레코드를 생성할 필요는 없습니다. 필요에 따라 기회 레코드를 생성하지 않을 수도 있습니다. 이는 리드 전환 과정에서 유연성을 제공합니다.

#### 3\. Lead Record는 사라짐(UI에서 보이지 않음. 조회 불가)

리드가 전환되면 해당 리드 레코드는 사용자 인터페이스(UI)에서 사라지고 일반적인 조회가 불가능합니다. **그러나 쿼리를 통해 여전히 해당 리드 레코드를 조회할 수 있습니다.** 쿼리로 조회하면 convert 관련 필드가 업데이트 된 것을 확인할 수 있습니다.

#### 4\. 리드 전환 상태를 나타내는 특별한 필드들

리드가 전환된 후에는 다음과 같은 특별한 필드들이 설정됩니다:

- ConvertedAccountId: 생성된 계정의 ID
- ConvertedContactId: 생성된 연락처의 ID
- ConvertedDate: 리드가 전환된 날짜
- ConvertedOpportunityId: 생성된 기회의 ID (해당하는 경우)
- IsConverted: 리드가 전환되었는지 여부를 나타내는 부울 값

## 주의 사항

#### 1\. Person Account

Person Account 레코드 타입이 활성화된 경우, Company 값이 null이면 리드는 Person Account로 전환됩니다. 반대로 Company 필드에 값이 있는 상태로는 Person Account로 전환하는 것은 불가능합니다.

#### 2\. 특정 필드는 코드로 업데이트 불가

몇몇 필드는 쓰기 불가능(not writable)하여 코드로 직접 업데이트할 수 없습니다. 이러한 필드는 ConvertLead를 사용해야만 업데이트할 수 있습니다. 해당 필드는 다음과 같습니다:

- ConvertedAccountId
- ConvertedContactId
- ConvertedDate
- ConvertedOpportunityId
- IsConverted

## 참고문서

- [https://help.salesforce.com/s/articleView?id=sf.faq_leads_what_happens_when.htm&type=5](https://help.salesforce.com/s/articleView?id=sf.faq_leads_what_happens_when.htm&type=5)
- [https://help.salesforce.com/s/articleView?id=sf.account_person_behavior.htm&type=5](https://help.salesforce.com/s/articleView?id=sf.account_person_behavior.htm&type=5)
- [https://developer.salesforce.com/docs/atlas.en-us.250.0.object_reference.meta/object_reference/sforce_api_objects_lead.htm](https://developer.salesforce.com/docs/atlas.en-us.250.0.object_reference.meta/object_reference/sforce_api_objects_lead.htm)
- [https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_dml_convertLead.htm](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_dml_convertLead.htm)
- [https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_calls_convertlead.htm](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_calls_convertlead.htm)
- [https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_leadstatus.htm?q=convert%20lead](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_leadstatus.htm?q=convert%20lead)
