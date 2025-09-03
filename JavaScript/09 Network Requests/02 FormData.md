FormData objects can help with sending HTML forms: with or without files, with additional fields and so on.
  
let formData = new FormData([form]);

If HTML form element is provided, it automatically captures its fields.

The special thing about FormData is that network methods, such as fetch, can accept a FormData object as a body. It’s encoded and sent out with Content-Type: multipart/form-data.

From the server point of view, that looks like a usual form submission.

<form id="formElem">
  <input type="text" name="name" value="John">
  <input type="text" name="surname" value="Smith">
  <input type="submit">
</form>

<script>
  formElem.onsubmit = async (e) => {
    e.preventDefault();
    
    //send formElem data and get response
    let response = await fetch('/article/formdata/post/user', {
      method: 'POST',
      body: new FormData(formElem)
    });
 
    let result = await response.json();
 
    alert(result.message);
  };
</script>

FormData Methods

We can modify fields in FormData with methods:

	• formData.append(name, value) – add a form field with the given name and value,
	• formData.append(name, blob, fileName) – add a field as if it were <input type="file">, the third argument fileName sets file name (not form field name), as it were a name of the file in user’s filesystem,
	• formData.delete(name) – remove the field with the given name,
	• formData.get(name) – get the value of the field with the given name,
	• formData.has(name) – if there exists a field with the given name, returns true, otherwise false

A form is technically allowed to have many fields with the same name, so multiple calls to append add more same-named fields.

There’s also method set, with the same syntax as append. The difference is that .set removes all fields with the given name, and then appends a new field. So it makes sure there’s only one field with such name, the rest is just like append:

	• formData.set(name, value),
	• formData.set(name, blob, fileName).

Also we can iterate over formData fields using for..of loop:

