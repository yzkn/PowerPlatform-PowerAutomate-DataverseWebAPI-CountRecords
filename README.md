# PowerPlatform-PowerAutomate-DataverseWebAPI-CountRecords

Power Automate クラウドフローで Dataverse テーブルのレコード数を取得する

---

## CountRecords

```
【Dataverseコネクタ／5000件以下】
	列を選択する：ya_memberid
	行のフィルター：createdon ne null

	length(outputs('Dataverseコネクタ／5000件以下_行を一覧にする')?['body/value'])



【Dataverseコネクタ／5000件超】5kから100k
	改ページ：オン
	しきい値：100000
	Fetch Xml クエリ：<fetch\n   page=\"@{variables('Dataverseコネクタ／5000件超 ページ番号')}\">   <entity name=\"ya_member\">\n    <attribute name=\"ya_memberid\"/>\n   </entity>\n</fetch>



【Dataverse Web API／5000件以下】
	GET https://orgfa5b0cd9.crm7.dynamics.com/api/data/v9.2/ya_members/?$select=ya_memberid&$count=true
	"Prefer" : "odata.include-annotations="Microsoft.Dynamics.CRM.totalrecordcount,Microsoft.Dynamics.CRM.totalrecordcountlimitexceeded""

	body('Dataverse_Web_API／5000件以下_JSONの解析')?['@odata.count']
	body('Dataverse_Web_API／5000件以下_JSONの解析')?['@Microsoft.Dynamics.CRM.totalrecordcount']
	body('Dataverse_Web_API／5000件以下_JSONの解析')?['@Microsoft.Dynamics.CRM.totalrecordcountlimitexceeded']



【Dataverse Web API／5000件超】5kから100k
	GET https://orgfa5b0cd9.crm7.dynamics.com/api/data/v9.2/ya_members/?fetchXml=@{encodeUriComponent('')}

	body('Dataverse_Web_API／5000件超_JSONの解析')?['value'][0]['count']



【Dataverse Unbound／5000件超】
	GET https://orgfa5b0cd9.crm7.dynamics.com/api/data/v9.2/RetrieveTotalRecordCount(EntityNames=["ya_member"])

	body('Dataverse_Unbound／5000件超_JSONの解析')?['EntityRecordCountCollection']?['Values']
```

## CountAllRecords_withPagingCookies

```
【行を一覧にする】
	<fetch page="@{variables('ページ番号')}" @{variables('ページングCookie')}>
	<entity name='ya_member'>
	<all-attributes/>
	</entity>
	</fetch>



【MoreRecordsの設定】
	@{if(empty(string(outputs('行を一覧にする')?['body']?['@Microsoft.Dynamics.CRM.morerecords'])), false, outputs('行を一覧にする')?['body']?['@Microsoft.Dynamics.CRM.morerecords'])}



【ページングCookieの設定】
	paging-cookie="@{if(
	empty(
		outputs('行を一覧にする')?['body']?['@Microsoft.Dynamics.CRM.fetchxmlpagingcookie']
	),
	'',
	replace(
		replace(
		replace(
			decodeUriComponent(
			decodeUriComponent(
				first(
				split(
					last(
					split(
						outputs('行を一覧にする')?['body']?['@Microsoft.Dynamics.CRM.fetchxmlpagingcookie'],
						'pagingcookie="'
					)
					),
					'" '
				)
				)
			)
			),
			'<',
			'&lt;'
		),
		'>',
		'&gt;'
		),
		'"',
		'&quot;'
	)
	)}"
```

## Solutions

- 1_0_0_0 CountRecordsのみ + CountAllRecords_withPagingCookiesフローパッケージ
- 1_0_1_0 CountAllRecords_withPagingCookiesを追加
- 1_0_2_0 CountAllRecords_withPagingCookiesのみ

---

Copyright (c) 2024 YA-androidapp(https://github.com/yzkn) All rights reserved.
