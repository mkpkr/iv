let userName = 'Max';

function greetUser(name) {
  let userName = name;
  alert(userName);
}

userName = 'Manu';
greetUser('Max');

This will actually show an alert that says 'Max' (NOT 'Manu').