### 공공데이터포털의 xml 데이터를 csv 파일로 변환하는 방법

데이터 분석 수업을 학교에서 듣고있는데 데이터 분석 기말과제를 위해 공공 데이터 포털의 xml 데이터를 csv 파일로 변환해보려고 한다. 

내 주제는 두 가지인데 

1.  코로나안심병원이 각 지역에 충분히 있을까?
2. 코로나 백신 접종률이 높은 지역에서는 확진자가 적게 나올까?

이다.



우선 필요한 데이터를 구한다.  나는 ```국민 안심병원현황```, ```공공데이터활용지원센터_보건복지부 코로나19 시·도발생 현황```, ```질병관리청_코로나19 예방접종 현황``` 을 필요한 데이터로 선정했다.

이 중에서 ```공공데이터활용지원센터_보건복지부 코로나19 시·도발생 현황```, ```질병관리청_코로나19 예방접종 현황```이 xml 데이터로 존재하는데  ```공공데이터활용지원센터_보건복지부 코로나19 시·도발생 현황``` 데이터는 따로 신청을 해야 볼 수 있는 데이터라 ```질병관리청_코로나19 예방접종 현황``` 데이터를 예시로 들어 설명하겠다. 

데이터를 찾고 URL을 클릭해 열어보면

```
This XML file does not appear to have any style information associated with it. The document tree is shown below.
<response>
<body>
<dataTime>2021.12.02 24:00:00</dataTime>
<items>
<item>
<sidoNm>서울특별시</sidoNm>
<firstCnt>3613</firstCnt>
<firstTot>7933770</firstTot>
<secondCnt>8315</secondCnt>
<secondTot>7698119</secondTot>
<thirdCnt>34397</thirdCnt>
<thirdTot>649999</thirdTot>
</item>
<item>
<sidoNm>부산광역시</sidoNm>
<firstCnt>1355</firstCnt>
<firstTot>2724073</firstTot>
<secondCnt>2942</secondCnt>
<secondTot>2631501</secondTot>
<thirdCnt>11161</thirdCnt>
<thirdTot>209295</thirdTot>
</item>
<item>
<sidoNm>대구광역시</sidoNm>
<firstCnt>925</firstCnt>
<firstTot>1895746</firstTot>
<secondCnt>2485</secondCnt>
<secondTot>1830009</secondTot>
<thirdCnt>7258</thirdCnt>
<thirdTot>130330</thirdTot>
</item>
<item>
<sidoNm>인천광역시</sidoNm>
<firstCnt>1331</firstCnt>
<firstTot>2430994</firstTot>
<secondCnt>3781</secondCnt>
<secondTot>2346905</secondTot>
<thirdCnt>9049</thirdCnt>
<thirdTot>183461</thirdTot>
</item>
<item>
<sidoNm>광주광역시</sidoNm>
<firstCnt>669</firstCnt>
<firstTot>1190287</firstTot>
<secondCnt>2190</secondCnt>
<secondTot>1143284</secondTot>
<thirdCnt>4852</thirdCnt>
<thirdTot>108004</thirdTot>
</item>
<item>
<sidoNm>대전광역시</sidoNm>
<firstCnt>659</firstCnt>
<firstTot>1173714</firstTot>
<secondCnt>1692</secondCnt>
<secondTot>1132714</secondTot>
<thirdCnt>5326</thirdCnt>
<thirdTot>92697</thirdTot>
</item>
<item>
<sidoNm>울산광역시</sidoNm>
<firstCnt>527</firstCnt>
<firstTot>908812</firstTot>
<secondCnt>1657</secondCnt>
<secondTot>879965</secondTot>
<thirdCnt>3311</thirdCnt>
<thirdTot>59007</thirdTot>
</item>
<item>
<sidoNm>세종특별자치시</sidoNm>
<firstCnt>146</firstCnt>
<firstTot>272339</firstTot>
<secondCnt>357</secondCnt>
<secondTot>261860</secondTot>
<thirdCnt>989</thirdCnt>
<thirdTot>22115</thirdTot>
</item>
<item>
<sidoNm>경기도</sidoNm>
<firstCnt>6180</firstCnt>
<firstTot>11154058</firstTot>
<secondCnt>16579</secondCnt>
<secondTot>10788551</secondTot>
<thirdCnt>39809</thirdCnt>
<thirdTot>876808</thirdTot>
</item>
<item>
<sidoNm>강원도</sidoNm>
<firstCnt>718</firstCnt>
<firstTot>1279589</firstTot>
<secondCnt>2076</secondCnt>
<secondTot>1237178</secondTot>
<thirdCnt>6373</thirdCnt>
<thirdTot>118575</thirdTot>
</item>
<item>
<sidoNm>충청북도</sidoNm>
<firstCnt>668</firstCnt>
<firstTot>1352302</firstTot>
<secondCnt>2529</secondCnt>
<secondTot>1307212</secondTot>
<thirdCnt>6536</thirdCnt>
<thirdTot>124993</thirdTot>
</item>
<item>
<sidoNm>충청남도</sidoNm>
<firstCnt>952</firstCnt>
<firstTot>1789964</firstTot>
<secondCnt>2610</secondCnt>
<secondTot>1725422</secondTot>
<thirdCnt>8595</thirdCnt>
<thirdTot>170692</thirdTot>
</item>
<item>
<sidoNm>전라북도</sidoNm>
<firstCnt>755</firstCnt>
<firstTot>1514759</firstTot>
<secondCnt>2705</secondCnt>
<secondTot>1460057</secondTot>
<thirdCnt>9037</thirdCnt>
<thirdTot>160277</thirdTot>
</item>
<item>
<sidoNm>전라남도</sidoNm>
<firstCnt>792</firstCnt>
<firstTot>1575741</firstTot>
<secondCnt>2498</secondCnt>
<secondTot>1520694</secondTot>
<thirdCnt>9319</thirdCnt>
<thirdTot>199860</thirdTot>
</item>
<item>
<sidoNm>경상북도</sidoNm>
<firstCnt>1255</firstCnt>
<firstTot>2160015</firstTot>
<secondCnt>2942</secondCnt>
<secondTot>2085263</secondTot>
<thirdCnt>9743</thirdCnt>
<thirdTot>193965</thirdTot>
</item>
<item>
<sidoNm>경상남도</sidoNm>
<firstCnt>1510</firstCnt>
<firstTot>2712518</firstTot>
<secondCnt>3997</secondCnt>
<secondTot>2614451</secondTot>
<thirdCnt>11699</thirdCnt>
<thirdTot>226671</thirdTot>
</item>
<item>
<sidoNm>제주특별자치도</sidoNm>
<firstCnt>450</firstCnt>
<firstTot>549615</firstTot>
<secondCnt>1033</secondCnt>
<secondTot>529163</secondTot>
<thirdCnt>2377</thirdCnt>
<thirdTot>43665</thirdTot>
</item>
</items>
</body>
</response>
```

이런식으로 나오는데 가장 윗줄인 This XML file does not appear to have any style information associated with it. The document tree is shown below.를 지우고 메모장에 저장한다.



메모장에 저장힌 후 xml 파일을 csv 파일로 바꿔주는 사이트에 그 파일을 올린다. 

사이트: https://data.page/xml/csv

그러면 excel 파일이나 csv 파일 형식으로 다운 받을 수 있다!

결과

![image](https://user-images.githubusercontent.com/52192706/144609941-db178c84-6415-4d13-9d79-3ebbf1f303e8.png)






