# form-submission-using-AJAX
## News-Letter form submission using fetch API and AJAX 


    /* ------------ HTML Form file: index.html  -------------------*/

    <form>
    <div class="field-group">
      <label for="IdName">Name:</label>
      <input name="name" id="IdName" type="text" required placeholder="Name">
    </div>
    <div class="field-group">
      <label for="IdEmail">E-mail:</label>
      <input name="email" id="IdEmail" type="text" required placeholder="E-mail">
    </div>
    <div class="field-group">
      <label for="IdQuestion">Question:</label>
      <textarea name="question" id="IdQuestion" required placeholder="Question"></textarea>
    </div> 
    <div class="captcha-group">
      Enter Text:
      <img src="captcha_image.php" id="IdCaptchaImg" />
      <input type="image" value="Reload" id="IdCaptchaReload" src="graphics/reloadTxt.png" />
      <input name="captcha_input" id="IdCaptchaInput" type="text" />                                                                                    
    </div>

    <div>
      <input value="Submit" id="IDsubmit" type="button">
    </div>
    <p id="MsgBox"></p>
    </form>



    /* ------------------- JavaScript File: main.js -----------*/

    // Global variables 
    // return element by querySelector
    var qs = function (el) { 
        return document.querySelector(el); 
    }
    // Global variables ends


    // Initializes an XMLHttpRequest 
    // For older browser if it don't support fetch API
    function xhrInit() { 
        if (window.XMLHttpRequest) {
          xmlhttp=new XMLHttpRequest();
        } else { 
          xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");	
        }
        return(xmlhttp); 
    }


    function submitForm(e) {

        e.preventDefault();

        let email = qs('#IdEmail').value; 
        let name = qs('#IdName').value;
        let question = qs('#IdQuestion').value; 
        let captcha = qs('#IdCaptchaInput').value;

        let msgBox = qs('#MsgBox');

        // normalize name
        if (typeof name !== 'string') {
          name = String(name);
         }

        // form validation
        if (email === ''|| name === '' || question === '' || captcha === '') { 
            msgBox.innerHTML = "All fields are required"; 
            return false; 
        }

        // name validation
        if (/[^a-z0-9\-#$%&'*+.^_`|~!]/i.test(name)) {
          msgBox.innerHTML = "Invalid name";
          return flase;
         }

        // email validation
        if (!(/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(email))) {      
            msgBox.innerHTML = "Invalid Email Id"; 
            return false; 
        }

        var formData = new FormData();
        // appending form data
        formData.append('email', email);
        formData.append('name', name);
        formData.append('question', question);
        formData.append('captcha_input', captcha);

        // checking browser suport for fetch API
        if (window.fetch) {

        // fetch() method used to fetch resource
        // fetch method returns a Promise that resolves to the Response (success or not)
            fetch('news-letter-form.php', {
                method: 'POST',  // GET, POST, PUT, DELETE, etc.
                body: formData 
                // body is an instance of any ofthe following types: 
                // ArrayBuffer, Blob/File, string, URLSearchParams, FormData
                // body data type must match "Content-Type" header, 
                // for FormData no need to include content-type in header.  
            }).then((response) => {

                if(response.ok) {
                    msgBox.innerHTML = 'Processing, Please Wait';

                    // To extract the JSON body content from the response, use json() method
                    // for raw text use text() method. 
                    // methods to extract a body: arrayBuffer(), blob(), json(), text(), formData()
                    response.text().then(info => {
                        if(info.err) {
                            console.log(err)
                        } else {
                            msgBox.innerHTML = info;
                        }
                    })
                } else {
                    msgBox.innerHTML = 'Network error';
                }

            })

        } else {

            xmlhttp = xhrInit(); 

            xmlhttp.onreadystatechange=function() { 
                msgBox.innerHTML = "Processing, Please Wait"; 

                if (xmlhttp.readyState==4 && xmlhttp.status==200) { 

                    // reloadCaptcha(); 

                    msgBox.innerHTML = xmlhttp.responseText; 
                }
            } 

            xmlhttp.open("POST","news-letter-form.php",true);
            xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded"); 
            xmlhttp.send(formData);
        }

    }

    qs('#IDsubmit').addEventListener('click', submitForm);
