let obj = {
  get propName() {
    // getter, the code executed on getting obj.propName
    return this._propName; //we typically use an internal name using _
  },
 
  set propName(value) {
    // setter, the code executed on setting obj.propName = value
    // can run validation, throw and error etc
    this._propName = value;
    return;
  }
};

obj.propName = "mike";
console.log(obj.propName);

Other example

let user = {
  name: "John",
  surname: "Smith",
 
  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};
 
alert(user.fullName); // John Smith
user.fullName = "Test"; // Error (property has only a getter)