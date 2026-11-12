<script setup>
	import { ref, onMounted, onBeforeUnmount } from 'vue';
	import axios from 'axios';

	import { Notyf } from 'notyf';
	import 'notyf/notyf.min.css';

	const notyf = new Notyf();

	const WEB3FORMS_ACCESS_KEY = "3f70a59a-1a3d-491f-9411-242b1f359baa";
	const SITE_KEY = "6LcMkBouAAAAAG8-U-VvBmD0Bx1dP8egPYSxhzUk";

	// Initial vlue for the email:
	const subject = "New message from Portfolio Contact Form";

	const name = ref("");
	const email = ref("");
	const message = ref("");

	const isLoading = ref(false);

	// Function for sending our email:
	const submitForm = async () => {
		if (!recaptchaToken.value) {
        	notyf.error('Please verify that you are not a robot.');
        	return;
	    }

		isLoading.value = true;

		try {
			const response = await axios.post("https://api.web3forms.com/submit", {
				access_key: WEB3FORMS_ACCESS_KEY,
				subject: subject,
				name: name.value,
				email: email.value,
				message: message.value
			},
			{
				headers: {
					'Content-Type': "application/json",
					Accept: "application/json"
				}
			});

			const result = response.data;

			if(result.success){
				notyf.success("Message Sent!");

				name.value = "";
				email.value = "";
				message.value = "";
			}

		} catch(error){
			notyf.error("Failed to send email!");
		} finally {
			isLoading.value = false;
		}
	}

	const recaptchaContainer = ref(null);
	const recaptchaWidgetId = ref(null);
	const recaptchaToken = ref('');

	// Callback called by reCAPTCHA when successful
	function onRecaptchaSuccess(token) {
		recaptchaToken.value = token;
	}

	// Callback when expired
	function onRecaptchaExpired() {
		recaptchaToken.value = '';
	}

	// Function to render the reCAPTCHA widget
	function renderRecaptcha() {
		if (!window.grecaptcha) {
			console.error('reCAPTCHA not loaded');
			return;
		}

		recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
			sitekey: SITE_KEY,
		size: 'normal', // or 'compact'
		callback: onRecaptchaSuccess,
		'expired-callback': onRecaptchaExpired,
	});
	}

	// Function to reset reCAPTCHA 
	function resetRecaptcha() {
		if (recaptchaWidgetId.value !== null) {
			window.grecaptcha.reset(recaptchaWidgetId.value);
			recaptchaToken.value = '';
		}
	}



	onMounted(() => {
	  // This code waits for the Google reCAPTCHA library to load, then renders the reCAPTCHA widget using onMounted hook. 
	  // The widget is rendered with grecaptcha.render(), which requires a sitekey. 
	  // Callback functions handle success and expiration events. 
	  // reCAPTCHA is reset upon form submission to clear the token.
		const interval = setInterval(() => {
			if (window.grecaptcha && window.grecaptcha.render) {
				renderRecaptcha();
				clearInterval(interval);
			}
		}, 100);

		onBeforeUnmount(() => {
			clearInterval(interval);
		});
	});

</script>

<template>
	<section id="contact" class="section-shell contact-section">
	    <div class="container">
	        <div class="section-heading">Contact</div>
	        <div class="row g-4 align-items-stretch">
	            <div class="col-lg-5">
	                <div class="map-card h-100 p-0">
	                    <iframe class="w-100 h-100" src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3330.6417697288293!2d122.06491171342424!3d6.9117689901815895!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x325041dd7a24816f%3A0x51af215fb64cc81a!2sWestern%20Mindanao%20State%20University!5e0!3m2!1sen!2sph!4v1790673070626!5m2!1sen!2sph" width="600" height="450" style="border:0;" allowfullscreen="" loading="lazy" referrerpolicy="strict-origin-when-cross-origin"></iframe>
	                </div>
	            </div>
	            <div class="col-lg-7">
	                <form class="contact-form h-100 d-flex flex-column justify-content-center"  @submit.prevent="submitForm">
	                    <div class="mb-2"><label class="visually-hidden" for="name">Contact Name</label><input class="form-control" id="name" name="name" v-model="name" type="text" placeholder="Contact Name"></div>
	                    <div class="mb-2"><label class="visually-hidden" for="email">Email</label><input class="form-control" id="email" name="email" v-model="email" type="email" placeholder="Email"></div>
	                    <div class="mb-3"><label class="visually-hidden" for="message">Message</label><textarea class="form-control message-field" id="message" name="message" v-model="message" placeholder="Message"></textarea></div>
	                    <div class="form-bottom d-flex align-items-center justify-content-between gap-3">
	                        <div class="social-links d-flex">
	                            <a class="d-inline-flex align-items-center justify-content-center" href="https://www.linkedin.com/in/keith-wilmer-handumon/" aria-label="LinkedIn"><i class="bi bi-linkedin"></i></a>
	                            <a class="d-inline-flex align-items-center justify-content-center" href="https://github.com/kimey31504" aria-label="GitHub"><i class="bi bi-github"></i></a>
	                        </div>
	                        <button class="btn btn-primary-custom" type="submit" :disabled="isLoading">{{ isLoading ? "Sending..." : "Submit" }}</button>
	                    </div>
		                <!-- recaptcha checkbox -->
	                    <div class="d-flex justify-content-end mt-2">
	                        <div ref="recaptchaContainer"></div>
	                    </div>
	                </form>
	            </div>
	        </div>
	    </div>
	</section>
</template>