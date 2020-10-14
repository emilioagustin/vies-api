<?php

namespace App\Http\Controllers;

use Illuminate\Routing\Controller as BaseController;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;

class ViesController extends BaseController
{
    public function validate($vatNumber, $countryCode) {
	$timeout = 30;
	if (!$vatNumber || !$countryCode) {
		return response()->json(['message' => 'Country code or vat number are empty'], 500);
	}

	$response = array ();
	$pattern = '/<(%s).*?>([\s\S]*)<\/\1/';
    	$keys = array (
            'countryCode',
            'vatNumber',
            'requestDate',
            'valid',
            'name',
            'address' 
    	);

    	$content = "<s11:Envelope xmlns:s11='http://schemas.xmlsoap.org/soap/envelope/'>
	  <s11:Body>
    	<tns1:checkVat xmlns:tns1='urn:ec.europa.eu:taxud:vies:services:checkVat:types'>
      	<tns1:countryCode>%s</tns1:countryCode>
      	<tns1:vatNumber>%s</tns1:vatNumber>
    	</tns1:checkVat>
  	</s11:Body>
	</s11:Envelope>";

    	$opts = array (
            'http' => array (
                    'method' => 'POST',
                    'header' => "Content-Type: text/xml; charset=utf-8; SOAPAction: checkVatService",
                    'content' => sprintf ( $content, $countryCode, $vatNumber ),
                    'timeout' => $timeout 
            ) 
    	);

    	$ctx = stream_context_create ( $opts );
    	$result = file_get_contents ('http://ec.europa.eu/taxation_customs/vies/services/checkVatService', false, $ctx );

    	if (preg_match ( sprintf ( $pattern, 'checkVatResponse' ), $result, $matches )) {
        	foreach ( $keys as $key )
            		preg_match ( sprintf ( $pattern, $key ), $matches [2], $value ) && $response [$key] = $value [2];
    	}
	return response()->json($response);
    }

}
