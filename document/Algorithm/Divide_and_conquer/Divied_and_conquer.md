# Divide and conquer

## 분할정복의 정의
분할정복의 패러다임은 한 문제를 유형이 비슷한 여러 개의 하위 문제로 나누어 재귀적으로 해결하고 이를 합쳐 원래 문제를 해결한다. 이 내용을 세 부분으로 나눠서 생각하면 아래와 같다.

1. 분할: 원래 문제를 분할하여 비슷한 유형의 더 작은 하위 문제들로 나누세요.
2. 정복: 하위 문제 각각을 재귀적으로 해결하세요. 하위 문제의 규모가 충분히 작으면 문제를 탈출 조건으로 놓고 해결하세요.
3. 합치기: 하위 문제들의 답을 합쳐서 원래 문제를 해결하세요.

![분할정복하는 이미지맵](https://github.com/msmasd/TIL/blob/master/document/Algorithm/Divide_and_conquer/image/Divide_and_conquer_map.PNG)

## 병합 정렬의 정의

정렬과정에서 분할 정복 알고리즘을 사용하기 때문에 하위 문제들을 어떻게 구성해야 하는지 결정해야한다. 궁극적인 목표는 전체 배열을 정렬하는 것입니다. 하위 문제는 하위 배열을 정렬하는 것이라고 합시다.
특히 하위 문제를 인덱스 p에서 시작하여 인덱스 r까지 정렬해야 하는 문제로 간주합니다.
하위 배열 표기는 array[p..r]
n개 요소를 가진 배열에 대해 원래 문제는 array[0..n-1]을 정렬하는 것이다.

1. 분할: p와 r의 중간 q를 찾습니다. p와 r을 더해서 2로 나눈 후 내림을 하여 정수로 만든다.
2. 정복: 분할 단계에서 만들어진 두 하위 문제 각각에 하위 배열을 재귀적으로 정렬합니다. 즉 하위 배열 array[p..q]를 재귀적으로 정렬하고 또 하위 배열 array[q+1..r]을 재귀적으로 정렬합니다.
3. 결합: 정렬된 두 하위 배열을 하나의 정렬된 하위 배열인 array[p..r]로 결합합니다.
4. 탈출조건: 탈출 조건은 2개 미만의 요소가 포함된 하위 배열로, 이는 p >= r인 경우입니다. 요소가 하나도 없거나 하나만 있는 하위 배열은 이미 정렬되어 있는 것과 같습니다. 그러므로 p < r일 때만 분할-정복-결합 과정을 거칩니다.

**예시**
[14, 7, 3, 12, 9, 11, 6, 2]가 있는 array일때 첫번째 하위 배열은 완전한 전체 배열 array[0..7]이기 때문에 하위 배열은 2개 이상의 요소를 가지므로 탈출조건에 맞지 않습니다.

1. 분할: q = 3
2. 정복: [14, 7, 3, 12]값이 포함된 array[0..3]과 [9, 11, 6, 2]가 포함된 array[4..7]을 정렬합니다. 정복 단계를 끝내면 각각의 두 하위 배열은 정렬이 되어 있습니다. 그러므로 완전한 배열은 [3, 7, 12, 14, 2, 6, 9, 11]이 됩니다.
3. 결합: 반으로 나눈 앞 쪽과 뒷 쪽으로 된 두 정렬된 하위 배열을 병합해서 마지막으로 정렬된 배열 [2, 3, 6, 7, 9, 11, 12, 14]를 만듭니다.

하위 배열 array[0..3]은 2개 이상의 요소로 되어 있기 때문에 탈출조건이 아니고, array[0..1](14, 7)과 array[2..3](3, 12)를 재귀적으로 정렬하여 [7, 14, 3, 12]값을 갖게 되는 array[0..3]을 얻어서 앞쪽 반과 뒷쪽 반을 병합하여 [3, 7, 12, 14]를 얻습니다.

또 하위 배열 array[0..1]은 array[0..0](14)와  array[1..1](7)d을 재귀적으로 정렬하여 여전히 [14, 7]을 포함하고 있는 array[0..1]을 얻고, 앞 쪽과 뒷 쪽을 병합하여 [7, 14]를 얻습니다.

![병합정렬하는 이미지맵](https://github.com/msmasd/TIL/blob/master/document/Algorithm/Divide_and_conquer/image/merge_sort_map.PNG)

위 사진처럼 동작한다.

## 병합정렬 구현

```javascript
// Takes in an array that has two sorted subarrays,
//  from [p..q] and [q+1..r], and merges the array
var merge = function(array, p, q, r) {
  // This code has been purposefully obfuscated,
  //  as you'll write it yourself in next challenge.
  var a=[],b=[],c=p,d,e;for(d=0;c<=q;d++,c++){a[d]=array[c];}for(e=0;c<=r;e++,c++){b[e]=array[c];}c=p;for(e=d=0;d<a.length&&e<b.length;){if(a[d]<b[e]){array[c]=a[d];d++;} else {array[c]=b[e]; e++;}c++; }for(;d<a.length;){array[c]=a[d];d++;c++;}for(;e<b.length;){array[c]=b[e];e++;c++;}
};


// Takes in an array and recursively merge sorts it
var mergeSort = function(array, p, r) {

  if (p < r && array.length > 1) {
      var q = floor((r-p)/2) + p;
      mergeSort(array, p, q);
      mergeSort(array, q+1, r);
      merge(array, p, q, r);
  }
};

var array = [14, 7, 3, 12, 9, 11, 6, 2];
mergeSort(array, 0, array.length-1);
Program.assertEqual(array, [2, 3, 6, 7, 9, 11, 12, 14]);

```

[참고](https://ko.khanacademy.org/computing/computer-science/algorithms/merge-sort/pc/challenge-implement-merge)