# 문제
정수를 요소로 갖는 배열을 입력받아 오름차순으로 정렬하여 리턴해야 합니다.

## 입력
### 인자 1 : arr
number 타입을 요소로 갖는 배열
arr[i]는 0 이상의 정수
arr.length 100,000 이하

## 출력
number 타입을 요소로 갖는 배열을 리턴해야 합니다.
배열의 요소는 오름차순으로 정렬되어야 합니다.
arr[i] <= arr[j] (i < j)

## 주의사항
기수 정렬을 구현해야 합니다.
arr.sort 사용은 금지됩니다.
입력으로 주어진 배열은 중첩되지 않은 1차원 배열입니다.

## 입출력 예시
```javascript
let output = radixSort([3, 1, 21]);
console.log(output); // --> [1, 3, 21]
```

## 힌트
* 기수 정렬(radix sort)은 내부적으로 계수 정렬(counting sort)을 사용합니다.
* 계수 정렬을 먼저 학습하고, 어떤 경우에 기수 정렬을 사용하는지 학습하도록 합니다.

## Advanced
arr[i]의 범위가 정수 전체로 확대될 경우, 기수 정렬 알고리즘을 완성해 보세요.

## 문제풀이
```javascript
// 최댓값
function getMax(arr) {
  return arr.reduce((max, item) => {
    if (item > max) return item;
    return max;
  }, 0);
}

function countingSort(arr, radix) { // arr = [3,1,21], radix = 1 (자릿수)
  const N = arr.length; // arr의 길이 3
  const output = Array(N).fill(0); // [0,0,0]
  const count = Array(10).fill(0); // [0,0,0,0,0,0,0,0,0,0]

  // 현재 자리수를 기준으로 0~9의 개수를 센다.
  arr.forEach((item) => {
    const idx = Math.floor(item / radix) % 10;
    count[idx]++; // [0,2,0,1,0,0,0,0,0,0]
  });

  // count[i]가 i까지의 누적 개수가 되도록 만든다.
  count.reduce((totalNum, num, idx) => {
    count[idx] = totalNum + num;
    return totalNum + num; 
  }); // [0,2,2,3,3,3,3,3,3,3]

  // 아래 속성이 유지되도록 하기 위해 배열을 거꾸로 순회한다.
  //  1. 가장 큰 값을 먼저 본다.
  //  2. 가장 큰 값을 가장 마지막에 놓는다.
  let i = N - 1;
  while (i >= 0) {
    const idx = Math.floor(arr[i] / radix) % 10;
    // count[idx]: 현재 radix의 idx까지 누적 개수
    // count[idx]개만큼 있으므로, 인덱스는 count[idx] - 1
    output[count[idx] - 1] = arr[i];
    count[idx] -= 1;
    i--;
  }

  return output; // [1,21,3]
}

// naive solution
// 양의 정수만 정렬 가능
// function radixSort(arr) {
//   const max = getMax(arr);
//   let radix = 1;
//   while (parseInt(max / radix) > 0) {
//     arr = countingSort(arr, radix);
//     radix *= 10;
//   }
//   return arr;
// }

// 음의 정수를 포함한 기수 정렬
// 1. 주어진 배열을 음수 부분과 양수 부분으로 나눈다.
// 2. 음수는 절대값을 기준으로, 즉 양수로 변환하여 기수 정렬한다.
// 3. 양수를 정렬한다.
// 4. 정렬된 음수 부분을 다시 음수로 바꾸고 순서를 뒤짚는다.
// 5. 음수 부분과 양수 부분을 붙인다.
function radixSort(arr) {
  let left = [];
  let right = [];
  arr.forEach((item) => {
    if (item >= 0) right.push(item);
    else left.push(item * -1);
  }); // 양수면 오른쪽에 음수면 양수로 바꿔서 왼쪽에 넣어준다.

  // 왼쪽의 최대값 구하기
  let max = getMax(left);
  let radix = 1;
  while (parseInt(max / radix) > 0) {
    left = countingSort(left, radix);
    radix *= 10;
  } // 왼쪽 정렬

  // 오른쪽의 최댓값 구하기
  max = getMax(right);
  radix = 1;
  while (parseInt(max / radix) > 0) {
    right = countingSort(right, radix);
    radix *= 10;
  } // 오른쪽 정렬

  return left
    .reverse()
    .map((item) => item * -1)
    .concat(right);
} // 정렬된 왼쪽값을 뒤집어서 다시 음수로 만들어준뒤 오른쪽 값들을 합쳐준다.

// My solution
function radixSort(arr) {
  let minus = [];
  let plus = [];
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < 0) {
      minus.push(arr[i]);
    } else {
      plus.push(arr[i]);
    }
  }
  // 재귀, 음수인 경우 역순으로 되어있기에 뒤집는 메소드 reverse 사용, 음수와 양수를 spread로 합친 배열 최종 리턴
  // 처음엔 1의 자릿수를 판단하기 위해 재귀 함수의 입력인자로 10과 1을 넣어준다.
  return [...recur(minus, 10, 1).reverse(), ...recur(plus, 10, 1)];
}

const recur = (arr, x, y) => { // x, y는 숫자의 자릿수를 구하기 위함
  if (y === 1000000) { // 입력받은 배열의 상한선
    return arr.flat();
  }

  // 길이 10의 2차원 배열
  let count = new Array(10).fill(0).map((el) => new Array(0));
  for (let i = 0; i < arr.length; i++) {
    // 음수 배열일 경우 절대값으로 변환
    count[Math.floor((Math.abs(arr[i]) % x) / y)].push(arr[i]);
  }
  // 재귀 실행을 위해 배열을 펼치고 입력 인자를 넣어준다.
  // 1의 자리를 모두 구하면 10의 자리를 구하기 위해 x, y 입력인자에 각각 10을 곱하여 재귀 호출
  return recur(count.flat(), x * 10, y * 10);
}

// 양의 정수와 음의 정수가 포함되는 기수정렬
// 1. 주어진 배열을 음수와 양수로 나눈다.
// 2. 음수는 절대값을 => 양수로 변환하여 기수 정렬하자.
// 3. 양수를 정렬한다.
// 4. 정렬된 음수 부분을 다시 음수로 바꾸고 순서를 뒤짚는다.
// 5. 음수 부분과 양수 부분을 이어 붙인다.

// <기수정렬>
// O(n + k)의 시간 복잡도 => k = 가장 큰 값
// k가 n보다 작은 수이면 O(n)이 되지만, k가 n보다 매우 큰 수이면 O(무한)이 될 수 있음
// 정렬할 수들의 최대값에 영향을 받는 알고리즘
// 숫자 크기에 시간복잡도가 매우 큰 영향을 받음 => 메모리 낭비
```
