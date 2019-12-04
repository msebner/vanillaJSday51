<!DOCTYPE html>
<html>
<head>
	<title>Form Autosave - Other Input Types</title>

	<style type="text/css">
		body {
			margin: 1em auto;
			max-width: 40em;
			width: 88%;
		}
		label,
		input,
		textarea,
		select {
			display: block;
			width: 100%;
		}
		[type="checkbox"],
		[type="radio"] {
			display: inline;
			width: auto;
		}
		input,
		textarea,
		select {
			margin-bottom: 1em;
		}
		textarea {
			height: 8em;
		}
	</style>
</head>
<body>

	<h1>Form Autosave - Other Input Types</h1>

	<form class="save-me" id="save-me">

		<label for="name">Name</label>
		<input type="text" name="name" id="name">

		<label for="address">Address</label>
		<input type="text" name="address" id="address">

		<label for="email">Email</label>
		<input type="email" name="email" id="email">

		<label for="hear-about-us">How did you hear about us?</label>
		<select name="hear-about-us" id="hear-about-us">
			<option value=""></option>
			<option value="google">Google</option>
			<option value="referral">Referred by a Friend</option>
			<option value="tv">A TV Ad</option>
			<option value="radio">A Radio Ad</option>
		</select>

		<label for="more">Additional thoughts?</label>
		<textarea name="more" id="more"></textarea>

		<p><strong>Do you agree to our terms of service?</strong></p>
		<label>
			<input type="radio" name="tos" value="yes">
			Yes
		</label>
		<label>
			<input type="radio" name="tos" value="no">
			No
		</label>

		<p><strong>Pick your favorite super heros.</strong></p>

		<label>
			<input type="checkbox" name="spiderman">
			Spiderman
		</label>

		<label>
			<input type="checkbox" name="wonderwoman">
			Wonder Woman
		</label>

		<label>
			<input type="checkbox" name="blackpanther">
			Black Panther
		</label>

		<p>
			<button type="submit">Submit</button>
		</p>

	</form>

	<script>

		;(function(){

		//
		// Variables
		//

		var storagePrefix = 'form-object-autosave';

		//Create an array of form inputs & textarea - available to all functions in the IIFE
		inputs = Array.prototype.slice.call(document.querySelectorAll('input, textarea, select'));

		//
		// Methods
		//

		/**
		 * Get an ID for a field
		 * @param  {Node}   field The field
		 * @return {String}       The ID
		 */
		 var getID = function (field) {

		 	if (field.id.length > 0) {
		 		return field.id;
		 	}

		 	if (field.name.length > 0) {
		 		return field.name;
		 	}

		 	return null;

		 };

		//Load the saved storage data on page load
		var loadInfo = function(){

			inputs.map(function(input){

				var id = getID(input);
				if (!id) return;

				// console.log(id);

				//Return the data from locaStorage
				var formData = localStorage.getItem(storagePrefix);
				if (!formData) return;

				// Convert the data from string to object
				formData = JSON.parse(formData);

				// If there's no formData stop the function
				if (!formData[id]) return;

				// If the input is a checkbox
				if (input.type === 'checkbox'){

					// if the localStorage data for the input is 'on'
					if (formData[id] === 'on'){

						// check the checkbox
						input.checked = true;
						return;
					}
				}

				// If the input is a radio button
				if (input.type === 'radio'){

					//If the radio button's value is 'yes'
					if (input.value === 'yes'){

						// And if the corresponding data in localStorage equals 'yes'
						if (formData[id] === 'yes'){

							// Check this radio button
							input.checked = true;

							return;
						}
					// But if the radio button's value is 'no'	
					} else if (input.value === 'no'){

						// and the corresponding data in localStorage equals 'no'
						if (formData[id] === 'no'){

							//Check this radio button
							input.checked = true;

							return;
						}
					}
				} else {
					input.value = formData[id];
				}

			}).join('');
		};

		//Clear out the localStorage
		var removeInfo = function(){
			localStorage.removeItem(storagePrefix);
		};

		//Log in form input to localStorage
		var inputHandler = function(event){

			// Only run for fields in the #save-me form
			if (!event.target.closest('#save-me')) return;

			var id = getID(event.target);
			if (!id) return;

			// Get any existing data from the formData local storage 
			var existing = localStorage.getItem(storagePrefix);

			// Parse any existing string data or, if there's no data, create an object
			existing = existing ? JSON.parse(existing) : {};

				// Set the Key/Value for the current form field
				existing[id] = event.target.value;

			// Add the current form field's value to the formData object as a string
			localStorage.setItem(storagePrefix, JSON.stringify(existing));
		};


		//If the Submit button is clicked removeInfo
		var submitHandler = function(event){

			if (event.target.id !== 'save-me') return;
			
			removeInfo();
		};

		//
		// Inits and Event Handlers
		//

		loadInfo();

		document.addEventListener('input', inputHandler, false);

		document.addEventListener('submit', submitHandler, false);

	})();

</script>
</body>
</html>
