function isInProdList(hostname) {
	var prodList = ['https://gpayments.webflow.io/'];
	return prodList.indexOf(hostname) > -1;
}
function isForbiddenEmailAddress(emailAddress) {
	var forbiddenList = ['@gmail.com', '@yahoo.com', '@outlook.com', '@hotmail.com'];
	for (var i = 0; i < forbiddenList.length; i++) {
		if (emailAddress.toLowerCase().indexOf(forbiddenList[i]) > -1)
			return true;
	}
	return false;
}

function validateEmailDomain($email = "[name='email']", $lblErrorMsg = "#lblCompanyEmail", $lang = "[name='locale']") {
	if (isForbiddenEmailAddress($($email).val())) {
		if ($($lang).val() === "en") {
			$($lblErrorMsg).removeClass('hidden').text('Please use your company email address.');
			return false;
		}
		if ($($lang).val() === "ja") {
			$($lblErrorMsg).removeClass('hidden').text('法人メールアドレスをご入力ください。');
			return false;
		}
		return false;
	}
	else {
		$($lblErrorMsg).addClass('hidden');
		return true;
	}
}

function validateEnquiryForm(form, $lang = "[name='locale']") {
	if (form.length === 1) {
		$(form).validate({
			submitHandler: function (form) {
				var defButton = $(form).find('button'),
						defButtonText = defButton.html();
				defButton.html('<i class="icon-line-loader icon-spin nomargin"></i>');
				var url = $(form).attr('action');

				$.ajax({
					method: "POST",
					url: url,
					data: $(form).serialize(),
					success: function (data) {
						defButton.removeClass('btn-primary btn-danger').addClass(
								'btn-success');

						window.location = "/?quoteReq=1";
					},
					error: function (xhr, text, error) {
						var err = JSON.parse(xhr.responseText);
						if (err.message === 'rate limit exceeded') {

							if ($($lang).val() === "en") {
								$('.rate-limit-exceeded').removeClass('hidden').html('The request limit has been reached.<br>Please try again later.');
							}
							else if ($($lang).val() === "ja") {
								$('.rate-limit-exceeded').removeClass('hidden').html('リクエストリミットに達しました<br>再度時間をおいてお試しください。');
							}

						} else {
							defButton.removeClass('btn-primary btn-success').addClass(
									'btn-danger');
							defButton.html(defButtonText);
						}
					}
				});
			}
		});
	}
}

var allowSubmit = false;
function captcha_filled() {
	allowSubmit = true;
}
function captcha_expired() {
	allowSubmit = false;
}

function validateContactForm() {
	let goog_snippet_vars = function () {
		var w = window;
		w.google_conversion_id = 1072549178;
		w.google_conversion_label = "mEl_CKmL2m8Qupq3_wM";
		w.google_remarketing_only = false;
	}

	let goog_report_conversion = function (url) {
		goog_snippet_vars();
		window.google_conversion_format = "3";
		var opt = new Object();
		opt.onload_callback = function () {
			if (typeof (url) != 'undefined') {
				window.location = url;
			}
		}
		var conv_handler = window['google_trackConversion'];
		if (typeof (conv_handler) == 'function') {
			conv_handler(opt);
		}
	}

	function checkRecaptcha(e) {
		let elementResult = $('.contact-form-result');
		if (!allowSubmit) {
			elementResult.removeClass('alert-danger alert-success').addClass('alert alert-danger').html("Please use the reCAPTCHA to verify that you're not a robot.").slideDown(400);
			return false;
		}
		else
			elementResult.hide();
		return true;
	}
	var $contactForm = $('.contact-widget.customjs');
	if ($contactForm.length > 0) {
		$contactForm.each(function () {
			var element = $(this),
				elementAlert = element.attr('data-alert-type'),
				elementResult = element.find('.contact-form-result');

			element.find('form').validate({
				submitHandler: function (form) {
					if (checkRecaptcha()) {
						elementResult.hide();

						$(form).find('.form-process').fadeIn();

						$(form).ajaxSubmit({
							target: elementResult,
							dataType: 'json',
							//contentType: 'application/json;charset=UTF-8',
							success: function (data) {
								$(form).find('.form-process').fadeOut();

								if (data.result) {
									elementResult.removeClass('alert-danger alert-success').addClass('alert alert-success').html('Your message was successfully sent and you will receive a confirmation email shortly.').slideDown(400);
									ga('send', 'event', 'contact-form', 'submit', '', '20');
									goog_report_conversion();
								}
								else
									elementResult.removeClass('alert-danger alert-success').addClass('alert alert-danger').html('There was an error in sending your message. Please try again.').slideDown(400);

								if ($(form).find('.g-recaptcha').children('div').length > 0) {
									grecaptcha.reset();
								}
								if (data.alert !== 'error') {
									$(form).clearForm();
								}

							},
							error: function (xhr, status, error) {
								$(form).find('.form-process').fadeOut();
								var err = JSON.parse(xhr.responseText);
								if (err.message === 'Free email domain name detected') {
									elementResult.removeClass('alert-danger alert-success').addClass('alert alert-danger').html('Invalid email address. Please use organisation email.').slideDown(400);
								}
								else if (err.message === 'rate limit exceeded') {
									elementResult.removeClass('alert-danger alert-success').addClass('alert alert-danger').html('Enquiry limit exceeded. Please try again later.').slideDown(400);
								}
								else
									elementResult.removeClass('alert-danger alert-success').addClass('alert alert-danger').html('There was an error in sending your message. Please try again.').slideDown(400);
							}
						});
					}
					window.scrollTo(0, 350);
				}
			});
		});
	}
}

function  removeVisibleClassFromAll() {
	$('.isVisible').removeClass('isVisible');
}
function setScroll(e){
	const scrollValues = {
		$offset         : $('#gp-scrollref').offset(),
		$width          : $('#gp-scrollref').innerWidth(),
		$maxScrollDiff : ( $('#gpsite-product-features-merchants').innerHeight() + $('#gpsite-product-features-issuers').innerHeight() + $('#gpsite-product-features-developers').innerHeight() )  - $('.gp-category-links').outerHeight() - 50,
		merchantTops    : $('#gp-ref-merchants').offset().top,
		issuerTops      : $('#gp-ref-card-issuers').offset().top,
		developersTops : $('#gp-ref-developers').offset().top
	}

	var $scrollDiff = $(window).scrollTop() - scrollValues.$offset.top;
	if($scrollDiff > scrollValues.$maxScrollDiff){
		//console.log('check')
		$('#gp-category-links')
				.css({'position': 'absolute', 'top': ( scrollValues.developersTops - scrollValues.$offset.top ), 'left': 15, 'width' : scrollValues.$width, 'z-index': 10})
	}
	if($scrollDiff > - 150  &&  $scrollDiff < scrollValues.$maxScrollDiff){
		$('#gp-category-links')
			.css({'position': 'fixed', 'top': 150, 'left': scrollValues.$offset.left, 'width' : scrollValues.$width, 'z-index': 100})
		// .animate({"marginTop": ($(window).scrollTop() )}, "slow" );}
	}
	else if($scrollDiff < - 50)
	{$('#gp-category-links')
		.css({'position': 'relative', 'top': '0', 'left': 0})
	}

	// if($(window).scrollTop() > scrollValues.developersTops){
	// 	console.log('check')
	// 	$('#gp-category-links')
	// 		.css({'position': 'absolute', 'bottom': 150, 'left': 15, 'width' : scrollValues.$width})
	// }
	if($(window).scrollTop() + 600 > scrollValues.developersTops){
		$('.gp-category-link').removeClass('active')

		$('#gp-developers-link').addClass('active')
	}
	else if($(window).scrollTop() + 500 > scrollValues.issuerTops){
		$('.gp-category-link').removeClass('active')

		$('#gp-issuer-link').addClass('active')

	}
	else if($(window).scrollTop() + 300 > scrollValues.merchantTops){
		$('.gp-category-link').removeClass('active')

		$('#gp-merchants-link').addClass('active')

	}


}
function gpMegaMenu() {
	$('.mega-menu > a ').on('click', function (e) {

		e.preventDefault();

		if(!($(this).hasClass('isVisible'))) {
			removeVisibleClassFromAll();
			$(this).next().addClass('isVisible');
			$(this).addClass('isVisible');
			$('.darkbackground').addClass('isVisible')
			var position = $(this).position();
			var width = $(this).width();
			$('.spotter').addClass('isVisible').animate({'margin-left': position.left+width/2-20}, 100, 'swing')
		}
		else
			removeVisibleClassFromAll();
	});
	$('#language-globe').on('click', function (e) {
		e.preventDefault();
		$(this).toggleClass('isVisible')
		$('#language-options').toggleClass('isVisible')
	})
	$(document).on("click", function(e) {
		if($(e.target).hasClass('mega-menuContents') || $(e.target).hasClass('darkbackground') || $(e.target).hasClass('mega-menu-content'))
			removeVisibleClassFromAll();
	});
}
function chkAgreeChange(chkElem) {
	if ($(chkElem).is(':checked'))
		$('input[type="submit"]').removeAttr('disabled');
	else
		$('input[type="submit"]').attr('disabled', 'disabled');
}
function setActiveCampaignTag(tagValue) {
	if(!tagValue) {
		var parts = window.location.pathname.replace("/index.html", "").split("/"); // Set folder name as tagValue if not mentioned
		tagValue = parts.pop();
		if(!tagValue)
			tagValue = parts.pop();
	}
	$('input[data-name="hiddentag"]').val(tagValue);
}
if (isInProdList(document.location.hostname)) {
	// ******************************************************* Add Google Analytic section
	(function (i, s, o, g, r, a, m) {
		i['GoogleAnalyticsObject'] = r; i[r] = i[r] || function () {
			(i[r].q = i[r].q || []).push(arguments)
		}, i[r].l = 1 * new Date(); a = s.createElement(o),
		m = s.getElementsByTagName(o)[0]; a.async = 1; a.src = g; m.parentNode.insertBefore(a, m)
	})(window, document, 'script', 'https://www.google-analytics.com/analytics.js', 'ga');

	ga('create', 'UA-1478114-1', 'auto');
	ga('send', 'pageview');

	// ******************************************************* GoogleTagManager
	(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
		new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
		j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
		'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
	})(window,document,'script','dataLayer','GTM-M9RKDWK');

	// ******************************************************* Crazyegg
	(function () {
		var a = document.createElement("script");
		var b = document.getElementsByTagName("script")[0];
		a.src = "//script.crazyegg.com/pages/scripts/0065/8363.js?" + Math.floor(new Date().getTime() / 3600000);
		a.async = true; a.type = "text/javascript"; b.parentNode.insertBefore(a, b)
	})();

	// ******************************************************* HubSpot
	(function () {
		var a = document.createElement("script");
		var b = document.getElementsByTagName("script")[0];
		a.id = "hs-script-loader";
		a.src = "//js.hs-scripts.com/3420715.js";
		a.async = true; a.defer = true; a.type = "text/javascript"; b.parentNode.insertBefore(a, b)
	})();

	// ******************************************************* Insight
	_linkedin_data_partner_id = "65778";
	(function () {
		var s = document.getElementsByTagName("script")[0];
		var b = document.createElement("script");
		b.type = "text/javascript"; b.async = true;
		b.src = "https://snap.licdn.com/li.lms-analytics/insight.min.js";
		s.parentNode.insertBefore(b, s);
	})();

	// ******************************************************* Add Google Analytic download tracker
	jQuery(document).ready(function ($) {
		var filetypes = /\.(zip|exe|pdf|doc*|xls*|ppt*)$/i;
		var baseHref = '';
		if (jQuery('base').attr('href') != undefined)
			baseHref = jQuery('base').attr('href');
		jQuery('a[target="_blank"]').each(function () {
			var href = jQuery(this).attr('href');
			if (href && href.match(filetypes)) {
				jQuery(this).click(function () {
					var extension = (/[.]/.exec(href)) ? /[^.]+$/.exec(href) : undefined;
					var filePath = href;
					ga('send', 'event', 'PDF', 'download', href);
				});
			}
		});
	});
}

// ******************************************************* Add Google Conversion for Whitepaper downloads
goog_snippet_vars = function () {
	var w = window;
	w.google_conversion_id = 1072549178;
	w.google_conversion_label = "VTzpCI7MzX0Qupq3_wM"; //Different from contact form label
	w.google_remarketing_only = false;
}
// DO NOT CHANGE THE CODE BELOW.
goog_report_conversion = function (url) {
	goog_snippet_vars();
	window.google_conversion_format = "3";
	var opt = new Object();
	opt.onload_callback = function () {
		if (typeof (url) != 'undefined') {
			window.location = url;
		}
	}
	var conv_handler = window['google_trackConversion'];
	if (typeof (conv_handler) == 'function') {
		conv_handler(opt);
	}	//Some Ad-blockers stop conversion
	else if (typeof (url) != 'undefined') {
		window.location = url;
	}
};


const formSubmitHandler = (formName, resourceName, winLocation, linkedIn = null) => {

    const form = $(`#${formName}`);

	form.on("submit", e => {
		e.preventDefault();
	})

    $.validator.setDefaults({
        submitHandler: (values) => {
            const url = $(values).attr('action');
            const data = $(values).serializeJSON();
            let newWin = window.open('', '_blank');

            data.url = location.href;

            $.ajax({
                url: url,
                async: false,
                data: data,
                type: 'POST',
                success: response => {

                    if (linkedIn) {
                        $('body').append(`<img height="1" width="1" style="display:none;" alt="" src=https://dc.ads.linkedin.com/collect/?pid=65778&conversionId=${linkedIn}&fmt=gif />`);
                    }

                    $('#downloadModal').modal('hide');
                    newWin.location = winLocation;
                    wpDownloaded(resourceName);
                },
                error: data => {
                    $('#downloadModal').modal('hide');
                    newWin.close();
                }
            })
        }
    });

    form.validate({})
};
