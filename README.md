# A Step-by-Step Guide: Integrate Razorpay payment gateway PHP website
Step 1: Create a Razorpay account
If you don’t have an account, sign up for a Razorpay account at
[https://dashboard.razorpay.com/]

![Demo](https://www.tutorialswebsite.com/wp-content/uploads/2024/02/Razorpay-Dashboard-840x419.png)

Step 2: Get API Keys
Log in to your Razorpay Dashboard.
Navigate to the “Account & Settings” > “API Keys” section to get your API key and secret key.


![Demo](https://www.tutorialswebsite.com/wp-content/uploads/2024/02/Razorpay-Dashboard-1-840x399.png)

Step 3: Create your HTML form to get the customer details
Let’s create a bootstrap normal form to get customer information and amount details.

Create an index.php page and Copy and Paste the following code into your index.php file.


<!DOCTYPE html>
<html>
<head>
<body style="background-repeat: no-repeat;">
<div class="container">
<div class="row">
<div class="col-xs-12 col-md-12">
                <div class="panel panel-default">
                    <div class="panel-heading">
                        <h4 class="panel-title">Charge Rs.10 INR  </h4>
                    </div>
                    <div class="panel-body">
                        <div class="form-group">
                            <label>Name</label>
                            <input type="text" class="form-control" name="billing_name" id="billing_name" placeholder="Enter name" required="" autofocus="">
                        </div>
                        <div class="form-group">
                            <label>Email</label>
                            <input type="email" class="form-control" name="billing_email" id="billing_email" placeholder="Enter email" required="">
                        </div>
                        
                  <div class="form-group">
                            <label>Mobile Number</label>
                            <input type="number" class="form-control" name="billing_mobile" id="billing_mobile" min-length="10" max-length="10" placeholder="Enter Mobile Number" required="" autofocus="">
                        </div>
                        
                         <div class="form-group">
                            <label>Payment Amount</label>
                            <input type="text" class="form-control" name="payAmount" id="payAmount" value="10" placeholder="Enter Amount" required="" autofocus="">
                        </div>
						
	<!-- submit button -->
	<button  id="PayNow" class="btn btn-success btn-lg btn-block" >Submit & Pay</button>
                       
                    </div>
                </div>
            </div>
</div>
</div>
</body>
</html>


![Demo](https://www.tutorialswebsite.com/wp-content/uploads/2024/02/How-to-Integrate-Razorpay-payment-gateway-in-PHP-tutorialswebsite-com.png)

Include the Razorpay Checkout script in your index.php file:

<script src="https://checkout.razorpay.com/v1/checkout.js"></script>

Now add the below script code in the index.php file, this code will act on the form submit button click. This is the Razorpay checkout js code which populates the payment popup form.


<script>
    //Pay Amount
    jQuery(document).ready(function($){

jQuery('#PayNow').click(function(e){

	var paymentOption='';
let billing_name = $('#billing_name').val();
	let billing_mobile = $('#billing_mobile').val();
	let billing_email = $('#billing_email').val();
  var shipping_name = $('#billing_name').val();
	var shipping_mobile = $('#billing_mobile').val();
	var shipping_email = $('#billing_email').val();
var paymentOption= "netbanking";
var payAmount = $('#payAmount').val();
			
var request_url="submitpayment.php";
		var formData = {
			billing_name:billing_name,
			billing_mobile:billing_mobile,
			billing_email:billing_email,
			shipping_name:shipping_name,
			shipping_mobile:shipping_mobile,
			shipping_email:shipping_email,
			paymentOption:paymentOption,
			payAmount:payAmount,
			action:'payOrder'
		}
		
		$.ajax({
			type: 'POST',
			url:request_url,
			data:formData,
			dataType: 'json',
			encode:true,
		}).done(function(data){
		
		if(data.res=='success'){
				var orderID=data.order_number;
				var orderNumber=data.order_number;
				var options = {
    "key": data.razorpay_key, // Enter the Key ID generated from the Dashboard
    "amount": data.userData.amount, // Amount is in currency subunits. Default currency is INR. Hence, 50000 refers to 50000 paise
    "currency": "INR",
    "name": "Tutorialswebsite", //your business name
    "description": data.userData.description,
    "image": "https://www.tutorialswebsite.com/wp-content/uploads/2022/02/cropped-logo-tw.png",
    "order_id": data.userData.rpay_order_id, //This is a sample Order ID. Pass 
    "handler": function (response){

    window.location.replace("payment-success.php?oid="+orderID+"&rp_payment_id="+response.razorpay_payment_id+"&rp_signature="+response.razorpay_signature);

    },
    "modal": {
    "ondismiss": function(){
         window.location.replace("payment-success.php?oid="+orderID);
     }
},
    "prefill": { //We recommend using the prefill parameter to auto-fill customer's contact information especially their phone number
        "name": data.userData.name, //your customer's name
        "email": data.userData.email,
        "contact": data.userData.mobile //Provide the customer's phone number for better conversion rates 
    },
    "notes": {
        "address": "Tutorialswebsite"
    },
    "config": {
    "display": {
      "blocks": {
        "banks": {
          "name": 'Pay using '+paymentOption,
          "instruments": [
           
            {
                "method": paymentOption
            },
            ],
        },
      },
      "sequence": ['block.banks'],
      "preferences": {
        "show_default_blocks": true,
      },
    },
  },
    "theme": {
        "color": "#3399cc"
    }
};
var rzp1 = new Razorpay(options);
rzp1.on('payment.failed', function (response){

    window.location.replace("payment-failed.php?oid="+orderID+"&reason="+response.error.description+"&paymentid="+response.error.metadata.payment_id);

         });
      rzp1.open();
     e.preventDefault(); 
}
 
});
 });
    });
</script>

In the above JS code, When the user clicks on the pay now button we will get the customer details like name, email, mobile, and price using JS. Also, you can see the Ajax code to process the Razorpay payment. When payment succeeds or fails you will get redirected to the page based on the above script redirection.

Step 4: Write the Ajax process to Create an order using Razorpay API

In this step, we’ve created the ‘submitpayment.php’ code file to manage AJAX post requests.

Within this file, you have the flexibility to handle database insertion tasks and create orders through Razorpay API calls.



<?php
header('Access-Control-Allow-Origin:*');
header('Access-Control-Allow-Methods:POST,GET,PUT,PATCH,DELETE');
header("Content-Type: application/json");
header("Accept: application/json");
header('Access-Control-Allow-Headers:Access-Control-Allow-Origin,Access-Control-Allow-Methods,Content-Type');

if(isset($_POST['action']) && $_POST['action']='payOrder'){

$razorpay_mode='test';

$razorpay_test_key='your_test_key'; //Your Test Key
$razorpay_test_secret_key='your_test_secret_key'; //Your Test Secret Key

$razorpay_live_key= 'Your_Live_Key';
$razorpay_live_secret_key='Your_Live_Secret_Key';

if($razorpay_mode=='test'){
    
    $razorpay_key=$razorpay_test_key;
    
$authAPIkey="Basic ".base64_encode($razorpay_test_key.":".$razorpay_test_secret_key);

}else{
    
	$authAPIkey="Basic ".base64_encode($razorpay_live_key.":".$razorpay_live_secret_key);
	$razorpay_key=$razorpay_live_key;

}

// Set transaction details
$order_id = uniqid(); 

$billing_name=$_POST['billing_name'];
$billing_mobile=$_POST['billing_mobile'];
$billing_email=$_POST['billing_email'];
$shipping_name=$_POST['shipping_name'];
$shipping_mobile=$_POST['shipping_mobile'];
$shipping_email=$_POST['shipping_email'];
$paymentOption=$_POST['paymentOption'];
$payAmount=$_POST['payAmount'];

$note="Payment of amount Rs. ".$payAmount;

$postdata=array(
"amount"=>$payAmount*100,
"currency"=> "INR",
"receipt"=> $note,
"notes" =>array(
	          "notes_key_1"=> $note,
	          "notes_key_2"=> ""
              )
);
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'https://api.razorpay.com/v1/orders',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>json_encode($postdata),
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json',
    'Authorization: '.$authAPIkey
  ),
));

$response = curl_exec($curl);

curl_close($curl);
$orderRes= json_decode($response);
 
if(isset($orderRes->id)){
 
$rpay_order_id=$orderRes->id;
 
$dataArr=array(
	'amount'=>$payAmount,
	'description'=>"Pay bill of Rs. ".$payAmount,
	'rpay_order_id'=>$rpay_order_id,
	'name'=>$billing_name,
	'email'=>$billing_email,
	'mobile'=>$billing_mobile
);
echo json_encode(['res'=>'success','order_number'=>$order_id,'userData'=>$dataArr,'razorpay_key'=>$razorpay_key]); exit;
}else{
	echo json_encode(['res'=>'error','order_id'=>$order_id,'info'=>'Error with payment']); exit;
}
}else{
    echo json_encode(['res'=>'error']); exit;
}
       ?>   



Step 5: Create a Payment Success Page
After a successful payment process, users will be redirected to a success page. In the index.php file mentioned above, once you receive the success response, you’ll need to redirect the user to the payment-success.php page.

Use the below payment-success.php file code

 

<?php 
if(isset($_GET)){
    
    echo "<pre>";
    print_r($_GET);
    echo "</p>";
}
?>



![demo](https://www.tutorialswebsite.com/wp-content/uploads/2024/02/razorpay-payment-gateway-integration.png)

Step 6: Create a Payment Failed Page
Use the below payment-failed.php file code


<?php 
if(isset($_GET)){
    
    echo "<pre>";
    print_r($_GET);
    echo "</p>";
}
?>

 





