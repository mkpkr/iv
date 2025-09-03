const INITIAL_GAME_BOARD = [
  [null, null, null],
  [null, null, null],
  [null, null, null],
];


let gameBoard = [...INITIAL_GAME_BOARD.map((array) => [...array])];

//updating gameBoard does not update INITIAL_GAME_BOARD, even its subarrays

Why does he map on a spread operator but at the bottom of Rest & Spread the example uses just a standard map (and in that example he said the spread was not necessary).