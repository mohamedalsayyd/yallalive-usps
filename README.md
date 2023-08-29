# Yallalive

## Added 'away' field to shop model
shops now have both 'open' and 'away' fields/attributes in their api json model.


## USPS Integrated routes :
### 1. Create domestic shipping labels.
- Route: `POST /api/usps/label/{orderId}/{ownerId}`
- Request:
```json
Content-Type: application/json
{
    "packageDescription": {
        "mailClass": "PRIORITY_MAIL", *required
        "rateIndicator": "SP", *required
        "weightUOM": "lb",
        "weight": 0.5, *required
        "dimensionsUOM": "in",
        "length": 9, *required
        "width": 0.25, *required
        "height": 6, *required
        "processingCategory": "MACHINABLE", *required
        "mailingDate": "2023-05-26", *required
        "extraServices": [
            920
        ],
        "destinationEntryFacilityType": "NONE", *required
    }
}
```
- Response:
```json
Content-Type: application/json
{
    "trackingNumber": "9205590109769900012613",
    "labelAddress": {
        "streetAddress": "STE 150",
        "secondaryAddress": "1100 WYOMING ST",
        "city": "SAINT LOUIS",
        "state": "MO",
        "ZIPCode": "63118",
        "ZIPPlus4": "2628",
        "firstName": "JOE",
        "lastName": "DOE",
        "ignoreBadAddress": false
    },
    "routingInformation": "420631182628",
    "postage": 7.64, //label payment amount
    "extraServices": [
        {
            "name": "USPS Tracking",
            "price": 0
        }
    ],
    "zone": "01",
    "commitment": {
        "name": "1 Days",
        "scheduleDeliveryDate": "2023-05-27"
    },
    "weightUOM": "LB",
    "weight": 0.5,
    "fees": [],
    "SKU": "DPXX0XXXXC01005"
}


Content-Type: application/pdf
File Name: label.pdf
Content-Encoding: gzip
Content-Transfer-Encoding: encode

{image data}
```
### 2. Create domestic returns labels.
- Route: `POST /api/usps/return-label/{orderId}/{ownerId}`
- Response: same as previous

### 3. Cancel domestic labels.
- Route: `DELETE /api/usps/label/{trackingNumber}`
- Request: NONE
- Response: 200 Successful Operation


### 4. Create international labels.
- Route: `POST /api/usps/international-label`
- Request:
```json
Content-Type: application/json
{
    "packageDescription": {
        "mailClass": "PRIORITY_MAIL_EXPRESS_INTERNATIONAL", *required (has enums)
        "rateIndicator": "SP", *required (has enums)
        "weightUOM": "lb",
        "weight": 0.5, *required
        "dimensionsUOM": "in",
        "length": 9, *required
        "width": 0.25, *required
        "height": 6, *required
        "processingCategory": "MACHINABLE", *required (has enums)
        "mailingDate": "2023-05-26", *required
        "extraServices": [
            920 //service code for enabling shipment tracking
        ],
        "destinationEntryFacilityType": "NONE", *required (has enums)
    },
    "customsForm": {
        "customsContentType": "MERCHANDISE", *required (has enums)
        "restrictionType": "QUARANTINE", *required
        "invoiceNumber": "389403", *required
        "licenseNumber": "string", *required
        "certificateNumber": "CER3629004", *required
        "AESITN": "4783947658", *required AES/ITN Exemption is a code that indicates the reason why you did not need to file electronic export information.
        "restrictionComments": "Covid",
        "contents": [
                {
                "itemDescription": "Lenovo Laptop", *required
                "itemQuantity": 1, *required
                "itemValue": 1, *required
                "weightUOM": "lb",
                "itemWeight": 1.0001, *required
                "countryofOrigin": "string[A-Z]{2}", *required
                }
            ]
    }
}
```
- Response:
```json
Content-Type: multipart/mixed
{

    "labelMetadata": {
    "labelAddress": {
    "streetAddress": "string",
    "streetAddressAbbreviation": "string",
    "secondaryAddress": "string",
    "cityAbbreviation": "string",
    "city": "string",
    "postalCode": "string",
    "province": "string",
    "country": "string",
    "countryISOAlpha2Code": "string",
    "firstName": "string",
    "lastName": "string",
    "firm": "string",
    "phone": "string"
    },
    "internationalTrackingNumber": "string",
    "SKU": "string",
    "postage": 0,
    "extraServices": [
            {
            "serviceID": "string",
            "serviceName": "string",
            "price": 0
            }
        ],
    "internationalPriceGroup": "string",
    "weightUOM": "lb",
    "weight": 5,
    "dimensionalWeight": "string",
    "fees": []
    },
    "labelImage": "string"
}

```
### 5. Cancel international labels.
- Route: `DELETE /api/usps/international-label/{rackingNumber}`
- Request: NONE
- Response: 200 Successful Operation
### 6. Track domestic/international labels.
- Route: `GET /api/usps/tracking/{trackingNumber}`
- Request: NONE
- Response:
```json
{
    "TrackResults": {
        "RequestSeqNumber": null,
        "TrackInfo": {
            "@ID": "XXXXXXXXXXXXXXXXXXXX",
            "TrackSummary": "USPS is now in possession of your item as of 7:31 am on February 15, 2023 in RICHMOND, VA 23227."
        }
    }
}
```

## Added payments to yallalive platform 
The following route should be used to charge sellers who bought shipping labels through the yallalive usps application
- Route: `POST /api/stripe/createPlatformIntent`
- Request:
```json
{
    "amount": "XXXXX",
}
```
- Response:
```json
{
    "response": "XXXX",
    "amount": "XXXXX",
    "client_secret": "XXXXXXXXXX"
}
```
## Added payment refund capability
- Route: `POST /api/stripe/refund`
include amount incase of partial refund, dont include amount incase of full refund
- Request:
```json
{
    "amount": "XXXXX",
    "payment_intent": "XXXXXXXXXX"
}
```
- Response:
```json
{
    "success": "XXXXX",
    "refund": {}
}
```
