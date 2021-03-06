# 문제
2차원 M x N 배열을 나선형(spiral)으로 순회해야 합니다.

## 입력
### 인자 1 : matrix
세로 길이(matrix.length)가 M, 가로 길이(matrix[i].length)가 N인 2차원 배열
matrix[i]는 string 타입을 요소로 갖는 배열
matrix[i][j].length는 1

## 출력
string 타입을 리턴해야 합니다.

## 주의사항
순회는 좌측 상단 (0,0)에서 시작합니다.
배열의 모든 요소를 순서대로 이어붙인 문자열을 리턴해야 합니다.

## 입출력 예시
```javascript
let matrix = [
  ['A', 'B', 'C'],
  ['D', 'E', 'F'],
  ['G', 'H', 'I'],
];
let output = spiralTraversal(matrix);
console.log(output); // --> 'ABCFIHGDE'

matrix = [
  ['T', 'y', 'r', 'i'],
  ['i', 's', 't', 'o'],
  ['n', 'r', 'e', 'n'],
  ['n', 'a', 'L', ' '],
];
output = spiralTraversal(matrix);
console.log(output); // --> 'Tyrion Lannister'
```

## 문제풀이
```javascript
const spiralTraversal = function (matrix) {
  let str = '';
  return recursive(matrix, str);
}

const recursive = (matrix, str) => {
  // base case
  if (matrix.length === 0) {
    return str;
  }
  
  const M = matrix.length; // 세로길이
  const N = matrix[0].length; // 가로길이


  // 가로 길이가 1이면, 모든 요소를 str에 더한다
  if (M === 1) {
    for (let j = 0; j < N; j++) {
      str += matrix[0][j];
    }
  }

  // 가로 길이가 1이 아니면, 외곽을 순회하여 str에 붙인다. (4번)
  if (M > 1) {
    for (let i = 1; i < 5; i++) {
      // 상
      if (i === 1) {
        for (let j = 0; j < N - 1; j++) {
          str += matrix[0][j];
        }
      }
      // 우
      if (i === 2) {
        for (let j = 0; j < M - 1; j++) {
          str += matrix[j][N - 1];
        }
      }
      // 하 (세로길이부터 1씩 감소)
      if (i === 3) {
        for (let j = N - 1; j > 0; j--) {
          str += matrix[M - 1][j];
        }
      }
      // 좌
      if (i === 4) {
        for (let j = M - 1; j > 0; j--) {
          str += matrix[j][0];
        }
      }
    }
  }

  //  재귀를 돌릴 새로운 배열 : 안쪽 행렬
  let result = [];
  for (let i = 1; i < M - 1; i++) {
    result.push(matrix[i].slice(1, -1));
  }
  // 위의 네번의 for문을 수행하고 외곽선을 제외한 새로운 배열을 다시 반복
  return recursive(result, str);
}

// reference
const spiralTraversal = function (matrix) {
  // 각 방향마다 row와 col의 변화를 저장
  const RIGHT = [0, 1];
  const DOWN = [1, 0];
  const LEFT = [0, -1];
  const UP = [-1, 0];
  // 각 방향을 위한 lookup table
  const MOVES = [RIGHT, DOWN, LEFT, UP];
  const M = matrix.length;
  const N = matrix[0].length;
  const isValid = (row, col) => row >= 0 && row < M && col >= 0 && col < N;

  let cnt = 0;
  let row = 0,
    col = -1;
  let direction = 0;
  let result = '';
  while (cnt < M * N) {
    const move = MOVES[direction];
    const [rd, cd] = move;

    row = row + rd;
    col = col + cd;
    while (isValid(row, col) && matrix[row][col] !== false) {
      result = result + matrix[row][col];
      // 한 요소를 두 번 접근하지 않게 하기 위해서, 접근된 요소를 false로 변경한다.
      matrix[row][col] = false;
      row = row + rd;
      col = col + cd;
      cnt++;
    }
    // row, col 이 행렬의 범위를 벗어났기 때문에,
    // 진행된 방향의 반대로 한 칸 이동한다.
    row = row - rd;
    col = col - cd;

    // 각 방향이 순환되기 때문에 모듈러 연산을 사용한다.
    direction = (direction + 1) % 4;
  }
  return result;
};

// 재귀 탈출 조건 : matrix.length === 0, matrix.length === 1
// 4번의 for문을 돌려서 상 -> 우 -> 하 -> 좌 순서로 문자열을 누적해 간다.
// 안쪽 행렬을 만들어 재귀 함수의 인자로 넣어준다.

// [a, b]
// [c, f]
// [h, i]
// [d, g]
```
