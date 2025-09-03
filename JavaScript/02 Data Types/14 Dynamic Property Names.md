Tic-tac-toe: object that contains 'X' and 'Y' player names.

     /**
       *symbol: 'X' or 'Y'
       * newName: the name of the palyer
      */
  function handlePlayerNameChange(symbol, newName) {
    setPlayers(prevPlayers => {
      return {
        ...prevPlayers,
        [symbol]: newName
      };
    });
  }