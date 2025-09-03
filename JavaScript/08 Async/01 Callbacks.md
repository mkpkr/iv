(See last section of Promise for how to do this with a Promise)


function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  
  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));
 
  document.head.append(script); //async
}



//calling the function
loadScript('/my/script.js', function(error, script) {
  if (error) {
    // handle error
  } else {
    // script loaded successfully
  }
});
