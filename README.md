## Seller Orders Menu: Shipping Page 
When the order's status is paid, the seller have two options(buttons): **Ship** and **Refund**.

**Ship** option should take the seller to a shipping page where seller can either 
1. purchase domestic/international shipping label.
2. add tracking number manually to order.


### 1. Purchasing a domestic/international shipping label.
1. Provide a form to the seller to collect order's shipping information:
- Incase of collecting shipping information for domestic label, use the following route to update order's information.

- **Request**:
```javascript
PUT api/orders/orders/{orderId}

Content-Type: application/json
{
  shippingDetails: {
      packageDescription: {
        weight: "number", //weight in pounds
        length: "number", //length in inches
        width: "number", //width in inches
        height: "number", //height in inches
        domesticMailClass: "string", // options: ["PRIORITY_MAIL", "PRIORITY_MAIL_EXPRESS", "PARCEL_SELECT", "PARCEL_SELECT_LIGHTWEIGHT"]
        internationalMailClass: "string", // options: ["FIRST-CLASS_PACKAGE_INTERNATIONAL_SERVICE", "PRIORITY_MAIL_INTERNATIONAL", "PRIORITY_MAIL_EXPRESS_INTERNATIONAL", "GLOBAL_EXPRESS_GUARANTEED"]
        processingCategory: "string", //options: ["FLATS", "MACHINABLE", "NON_MACHINABLE"],
        rateIndicator: "string", //rateIndicator options: [DR - SP] (DR - Dimensional, Rectangular SP - Single Piece)
    }
  }
}
```
- Incase of collecting shipping information for international label, use the following route:
```javascript
PUT api/orders/orders/{orderId}

Content-Type: application/json
{
  shippingDetails: {
      packageDescription: {
        weight: "number", //weight in pounds
        length: "number", //length in inches
        width: "number", //width in inches
        height: "number", //height in inches
        domesticMailClass: "string", // options: ["PRIORITY_MAIL", "PRIORITY_MAIL_EXPRESS", "PARCEL_SELECT", "PARCEL_SELECT_LIGHTWEIGHT"]
        internationalMailClass: "string", // options: ["FIRST-CLASS_PACKAGE_INTERNATIONAL_SERVICE", "PRIORITY_MAIL_INTERNATIONAL", "PRIORITY_MAIL_EXPRESS_INTERNATIONAL", "GLOBAL_EXPRESS_GUARANTEED"]
        processingCategory: "string", //options: ["FLATS", "MACHINABLE", "NON_MACHINABLE"],
        rateIndicator: "string", //rateIndicator options: [DR - SP] (DR - Dimensional, Rectangular SP - Single Piece)
    }
  }
}
```

 2. Use (Calculate Domestic Label Price) or (Calculate International Label Price) to get the total price for shipping label and make payment for seller.
 3. If payment is successfully completed, use (Create Domestic Label) or (Calculate International Label Price) to create the shipping label for the seller. 
 > Note that the created shipping label's tracking number will be automatically saved to the order's information for further use.

---

## Yallalive Shipping Labels API

### Calculate Domestic Label Price:
Use the following route to calculate the shipping label price for an order, It should return an estimated price amount to be displayed to the buyer in the shipping page before confirming the checkout.

- **Request**:
```javascript
POST api/usps/domestic-label/price

Content-Type: application/json
{
  shippingAddressId: "XXXXXXXXXX",
  productId: "XXXXXXXXXX"
}
```
- **Response**:
```javascript
Content-Type: application/json
{
    "totalPrice": 11.06
}
```

### Calculate International Label Price:

- **Request**:
```javascript
POST api/usps/international-label/price

Content-Type: application/json
{
  shippingAddressId: "XXXXXXXXXX",
  productId: "XXXXXXXXXX"
}

```
- **Response**:
```javascript
Content-Type: application/json
{
    "totalPrice": 14.96
}
```

---

### Create Domestic Label:

- **Request**:
```javascript
POST api/usps/domestic-label

Content-Type: application/json
{
  orderId: "XXXXXXXXXX",
}
```
- **Response**:
```javascript
Content-Type: application/json
{
    trackingNumber: "9205590109769900012613",
    labelAddress: {
        streetAddress: "STE 150",
        secondaryAddress: "1100 WYOMING ST",
        city: "SAINT LOUIS",
        state: "MO",
        ZIPCode: "63118",
        ZIPPlus4: "2628",
        firstName: "JOE",
        lastName: "DOE",
        ignoreBadAddress: false
    },
    routingInformation: "420631182628",
    postage: 7.64,
    extraServices: [
        {
            name: "USPS Tracking",
            price: 0
        }
    ],
    zone: "01",
    commitment: {
        name: "1 Days",
        scheduleDeliveryDate: "2023-05-27"
    },
    weightUOM: "LB",
    weight: 0.5,
    fees: [],
    SKU: "DPXX0XXXXC01005"
}

Content-Type: application/pdf
File Name: label.pdf
Content-Encoding: gzip
Content-Transfer-Encoding: encode
{file data}
```

### Create Returns Domestic Label:

- **Request**:
```javascript
POST api/usps/return-label

Content-Type: application/json
{
  orderId: "XXXXXXXXXX",
}
```
- **Response**: same as previous.

### Cancel Domestic Label:

- **Request**:
```javascript
DELETE api/usps/domestic-label/{trackingNumber}
```
- **Response**: operation successful

### Track both Domestic and International label
- **Request**:
```javascript
GET api/usps/tracking/{trackingNumber}
```
- **Response**:
```javascript
{
    TrackResults: {
        RequestSeqNumber: null,
        TrackInfo: {
            @ID: "XXXXXXXXXXXXXXXXXXXX",
            TrackSummary: "USPS is now in possession of your item as of 7:31 am on February 15, 2023 in RICHMOND, VA 23227."
        }
    }
}
```
