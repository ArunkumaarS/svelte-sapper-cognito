<script>
    import swal from 'sweetalert';
    import { Auth } from 'aws-amplify';
    import { CognitoUser } from 'amazon-cognito-identity-js';
    let phoneNumber, otp;
    let cognitoUser;
    let isExistingUser = false;
    async function getOtp(){
        let err = '';
        if(phoneNumber === ''){
            err = 'Phone Number cannot be empty'
        } else if (phoneNumber.length <10 && phoneNumber.length >10){
            err= 'Phone number should be of 10 digits'
        }

        if(err != ''){
            swal({
                title: 'oops!!',
                text: err,
                icon: 'error',
                button: 'Enter a valid number',
            });
        } else {
            phoneNumber = '+91'+phoneNumber
            try {

                cognitoUser = await Auth.signUp(phoneNumber, '00000000')
                cognitoUser = await Auth.signIn(phoneNumber);
            } catch (err) {

                if (err.code === "UsernameExistsException"){
                    cognitoUser = await Auth.signIn(phoneNumber);
                }

            }       
        }
    }

    async function submitOtp(){
            try {
                cognitoUser = await Auth.sendCustomChallengeAnswer(cognitoUser, otp);
            } catch (err) {
                console.log(err)
            }       
    }
</script>

<svelte:head>
	<title>Sapper project template</title>
</svelte:head>

<h1>Login/Signup using phone number</h1>
<input bind:value ={phoneNumber}/>
<button on:click={getOtp}>Get OTP</button>
<h1>Enter the otp</h1>
<input bind:value = {otp}/>
<button on:click={submitOtp}>Submit OTP</button>

