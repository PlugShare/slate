---
title: PlugShare API Reference

language_tabs:
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the PlugShare API! You can use our API to access PlugShare API endpoints, which can get information on charging stations, reviews, and photos in our database.

## Using The API

Generally, you'll want to use a search method to return a list of locations. Each location will contain one or more stations, each of which may contain one or more outlets. Not all information about a location is returned from the search method; for reviews, photos, and more you'll want to request the location directly using its unique identifier.

## Access Instructions

URL: [https://api.plugshare.com/](https://api.plugshare.com/)

HTTPS + HTTP Basic auth is required when making API requests. Your username and password will be provided.

# Authentication

> To authorize, use this code:

```javascript
const plugshare = require('plugshare');

let api = plugshare.authorize('plugshareapi');
```

> Make sure to replace `plugshareapi` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: plugshareapi`

<aside class="notice">
You must replace <code>plugshareapi</code> with your personal API key.
</aside>

# Filters

You may want to apply some filters to the search queries to return only relevant stations. Filters are added as query parameters (for GET requests) or JSON fields (for POST requests).

## Outlets

The 'outlets' filter is a JSON formatted list of "outlet descriptor" objects. Each object has two optional properties, connector and power. Locations will only be returned if they have a station that contains one or more outlets that match one of these descriptors. A descriptor matches an outlet if each of the descriptor properties matches the corresponding property of the outlet. See [Outlet Connector Types](#outlet)

## Cost

* 'true' (default) = Returns all locations regardless of cost
* 'false' = Does not return locations with a known fee (like parking or $/kw)

## Access

A comma separated list of access values. e.g. "1,2,3"

* 1 = A public location
* 2 = A restricted location. Not available for public use.
* 3 = A residential location that has been shared. Not available for public use without prior permission.

## Availability

A comma separated list of availability values. e.g. "0, 1,2,3"

* 0 = Unknown
* 1 = Available
* 2 = In Use
* 3 = Offline

## Amenities

Only returns locations that have certain amenities. A comma separated list of amenity id's available at the location. e.g. "0, 1,2,3"

* 1 = Hotel/Lodging

## Networks to exclude

A comma separated list of network ids. Passing "exclude_networks=1" will exclude locations which exclusively have ChargePoint stations. If a location has both a ChargePoint station and a station of another network or a non-networked station it will still be eligible to be returned. See [Networks List](#network)

## Networks to exclusively include

A comma separated list of network ids. Passing "networks=3,4" will only include locations with either SemaCharge or GE WattStation stations. See [Networks List](#network)

# Locations

A driver identifiable destination where Charging Stations (Stations) are located. As a general rule if stations are within visible range of each other they are organized together into a location.

## Get All Locations

```javascript
const plugshare = require('plugshare');

let api = plugshare.authorize('plugshareapi');
let locations = api.locations.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all locations.

### HTTP Request

`GET https://api.plugshare.com/locations`

## Object Properties

<table>
  <tr>
    <th>Parameter</th>
    <th>Description</th>
    <th>Can be null?</th>
  </tr>
  <tr>
    <td>
      <div class="field">id</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>unique and immutable identifier</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">name</div>
      <div class="type">String</div>
    </td>
    <td>Descriptive name of the parking structure, business etc.</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">icon</div>
      <div class="type">String (URL)</div>
    </td>
    <td>A recommended icon for displaying the location</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">latitude</div>
      <div class="type">Number</div>
    </td>
      <td></td>
      <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">longitude</div>
      <div class="type">Number</div> 
    </td>
      <td></td>
      <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">created_at</div>
      <div class="type">String (timestamp)</div>
    </td>
    <td>ISO 8601 Format  "yyyy'-'MM'-'dd'T'HH':'mm':'ss'Z'"</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">description</div>
      <div class="type">String</div>
    </td>
    <td>Unicode string describing a location can include HTML elements</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">stations</div>
      <div class="type">Array</div>
    </td>
    <td>Contains station objects. Each location will have at least 1 station object.</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">reviews</div>
      <div class="type">Array</div>
    </td>
    <td>Contains review objects. Ordered from the most recent to oldest</td>
    <td>Can be Empty</td>
  </tr>
  <tr>
    <td>
      <div class="field">score</div>
      <div class="type">Number (decimal)</div>
    </td>
    <td>The location’s PlugScore - If a location has enough data to be scored this is a decimal number with 1 significant digit from 0.0 (worst) - 10.0 (best). Score is based on recency weighted reviews and may also be affected by cost, ailable power and other factors</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">custom_ports</div>
      <div class="type">String</div>
    </td>
    <td>Lists any non-standard outlets including legacy charging standards no longer being produced. Example include Large Paddle and Small Padde Inductive</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">enabled</div>
      <div class="type">Boolean</div>
    </td>
    <td>If false the location will not be returned by search queries and should no longer be considered in service. Disabled locations can still be retrieved directly via a REST call.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">cost</div>
      <div class="type">Boolean</div>
    </td>
    <td>cost (Boolean) - if true, then there is a cost associated with this location (check cost_description). if false, then cost is unknown. this field supplements cost values at each station location. generally cost values at stations are ovided by 3rd parties, while cost values at locations are provided by PlugShare members</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">cost_description</div>
      <div class="type">String</div>
    </td>
    <td>A description of fees for charging and parking at this location</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">access</div>
      <div class="type">Number (integer enum)</div>
    </td>
    <td>1 = A public location<br>2 = A restricted location. Not available for general public use. Examples include workplace charging and car dealerships with charging policy restrictions.<br>3 = A residential location that has been shared. Not available for public use without prior permission.</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">url</div>
      <div class="type">String (URL)</div>
    </td>
    <td>A shareable link that redirects to this PlugShare location (platform aware: opens apps if user is on mobile, otherwise directs to web)</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">phone</div>
      <div class="type">String</div>
    </td>
    <td>Formatting of this field is not currently guaranteed</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">address</div>
      <div class="type">Yes</div>
    </td>
    <td>Address as provided by network, driver, or adjusted by editor</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">reverse_geocoded_address</div>
      <div class="type">String</div>
    </td>
    <td>Formatted Address string as reverse geocoded from the latitude/longitude</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">reverse_geocoded_address_components</div>
      <div class="type">Array</div>
    </td>
    <td>List of geocoded components (See https://developers.google.com/maps/documentation/geocoding/#Types for definitions)</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">payment_enabled</div>
      <div class="type">Boolean</div>
    </td>
    <td>True if this location accepts Pay with PlugShare payments</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">valid_outlets</div>
      <div class="type">Array</div>
    </td>
    <td>Deprecated - The types of outlets that are valid for this location’s locale. This is not a list of outlet types that are present.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">nissan_nctc</div>
      <div class="type">Boolean</div>
    </td>
    <td>True if at least one station at the location qualifies for Nissan’s No Charge To Charge Program</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">qr_enabled</div>
      <div class="type">Boolean</div>
    </td>
    <td>True if this station is tagged with a QR code for station identification and activation flows</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">pwps_version</div>
      <div class="type">Number (Integer)</div>
    </td>
    <td>The Pay with PlugShare version protocol required to be supported by the client to activate this station</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">compass_bearing</div>
      <div class="type">Number (Float)</div>
    </td>
    <td>A compass bearing from the specified address or coordinate.  Note: this field will only appear for locations returned by the /locations/nearby endpoint.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">distance_meters</div>
      <div class="type">Number (Float)</div>
    </td>
    <td>A distance in meters from the specified address or coordinate.  Note: this field will only appear for locations returned by the /locations/nearby endpoint.</td>
    <td>Yes</td>
  </tr>
</table>

# Station

A physical charging unit installed on the ground or wall. In EV industry terms this is also frequently referred to as an EVSE (Electric Vehicle Service Equipment). Stations can be network managed. Stations frequently support near time availability data.

## Object Properties

<table>
  <tr>
    <th>Property</th>
    <th>Description</th>
    <th>Can be null?</th>
  </tr>
  <tr>
    <td>
      <div class="field">id</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>Unique and immutable identifier</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">created_at</div>
      <div class="type">String (date)</div>
    </td>
    <td>ISO 8601 Format  "yyyy'-'MM'-'dd'T'HH':'mm':'ss'Z'"</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">network</div>
      <div class="type">Object</div>
    </td>
    <td>The network that owns or directly manages this station</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">network_ext_id</div>
      <div class="type">String</div>
    </td>
    <td>The unique identifier on the parent network. Used to cross reference against 3rd party database. IDs between different networks can collide but used with the network property this is guaranteed to be unique.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">cost</div>
      <div class="type">Number (integer enum)</td>
    </div>
    <td>0=Unknown, 1=Free, 2=Fee</div>
    <td>No</td>
  </td>
  <tr>
    <td>
      <div class="field">cost_description</div>
      <div class="type">String</div>
    </td>
    <td>Describes the cost of charging at this location. Examples include $2/hr, $0.10/kWh, $8 per session.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">manufacturer</div>
      <div class="type">String</div>
    </td>
    <td>The manufacturer of this station</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">model</div>
      <div class="type">String</div>
    </td>
    <td>The hardware model type</td>
    <td>Yes</td>
  </tr>
</table>

# Outlet

A physical vehicle-to-station connection point: either a plug or a socket. Stations can have multiple outlets. Stations can have outlets of varying types.

## Object Properties

<table>
  <tr>
    <th>Property</th>
    <th>Description</th>
    <th>Can be null?</th>
  </tr>
  <tr>
    <td>
      <div class="field">id</div>
      <div class="type">number (integer)</div>
    </td>
    <td>Unique and immutable identifier</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">connector</div>
      <div class="type">Number (integer enum)</div>
    </td>
    <td>See Outlet Connector Types</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">Power</div>
      <div class="type">Number (integer enum)</div>
    </td>
    <td>See Outlet Connector Types</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">available</div>
      <div class="type">string</div>
    </td>
    <td>If null, outlet level availability is unknown (Note: generally, see Station availability instead)</td>
    <td>Yes</td>
  </tr>
</table>

## Outlet Connector Types

PlugShare supports all EV charging connectors that are in active use globally.

<table class="connectors">
  <tr>
    <th>Connector ID</th>
    <th>Power Level</th>
    <th>Description</th>
    <th>Image</th>
    <th>North America</th>
    <th>UK</th>
    <th>EU</th>
    <th>JP</th>
    <th>AU</th>
  </tr>
  <tr>
    <td>1</td>
    <td></td>
    <td>US Wall Outlet</td>
    <td><img src="http://developers.plugshare.com/images/image03.png"></td>
    <td>Y</td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
  </tr>
  <tr>
    <td>2</td>
    <td></td>
    <td>J-1772</td>
    <td><img src="http://developers.plugshare.com/images/image08.png"></td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td>3</td>
    <td></td>
    <td>CHAdeMO</td>
    <td><img src="http://developers.plugshare.com/images/image04.png"></td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td>4</td>
    <td></td>
    <td>Tesla Roadster</td>
    <td><img src="http://developers.plugshare.com/images/image00.png"></td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td>5</td>
    <td></td>
    <td>NEMA 14-50</td>
    <td><img src="http://developers.plugshare.com/images/image10.png"></td>
    <td>Y</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>6</td>
    <td>0</td>
    <td>Tesla S HPWC</td>
    <td><img src="http://developers.plugshare.com/images/image05.png"></td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>6</td>
    <td>1</td>
    <td>Tesla Supercharger</td>
    <td><img src="http://developers.plugshare.com/images/image05.png"></td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>7</td>
    <td></td>
    <td>Type 2 (Mennekes)</td>
    <td><img src="http://developers.plugshare.com/images/image06.png"></td>
    <td></td>
    <td>Y</td>
    <td>Y</td>
    <td></td>
    <td>Y</td>
  </tr>
  <tr>
    <td>8</td>
    <td></td>
    <td>Type 3</td>
    <td><img src=""></td>
    <td></td>
    <td>Y</td>
    <td>Y</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>9</td>
    <td></td>
    <td>BS1363</td>
    <td><img src="http://developers.plugshare.com/images/image09.png"></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>10</td>
    <td></td>
    <td>Europlug</td>
    <td><img src="http://developers.plugshare.com/images/image13.png"></td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>11</td>
    <td></td>
    <td>UK Commando</td>
    <td><img src="http://developers.plugshare.com/images/image12.png"></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>12</td>
    <td></td>
    <td>AS3112</td>
    <td><img src="http://developers.plugshare.com/images/image01.png"></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Y</td>
  </tr>
  <tr>
    <td>13</td>
    <td></td>
    <td>SAE Combo DC CCS</td>
    <td><img src="http://developers.plugshare.com/images/image02.png"></td>
    <td>Y</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>Y</td>
  </tr>
  <tr>
    <td>14</td>
    <td></td>
    <td>Three Phase 32A (UK)</td>
    <td><img src=""></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>

# Network

A company that operates stations and provides subscription services or access control services for those stations.

## Object Properties

<table>
  <tr>
    <th>Property</th>
    <th>Description</th>
    <th>Can be null?</th>
  </tr>
  <tr>
    <td>
      <div class="field">id</div>
      <div class="type">number (integer)</div>
    </td>
    <td>Unique and immutable identifier</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">name</div>
      <div class="type">string</div>
    </td>
    <td>The driver-friendly name for the Network’s Brand such as “SemaCharge”</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">description</div>
      <div class="type">string</div>
    </td>
    <td>A short marketing summary of the Network’s service offerings.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">action_name</div>
      <div class="type">string</div>
    </td>
    <td>A description of the URL destination. Examples include “Sign Up” or “Learn More”</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">action_url</div>
      <div class="type">string (URL)</div>
    </td>
    <td>A URL generally to aid in registration with a network</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">phone</div>
      <div class="type">string</div>
    </td>
    <td>Phone number for customer service or activation by phone requests</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">image</div>
      <div class="type">string (URL)</div>
    </td>
    <td>If available, an approved logo for the Network.</td>
    <td>Yes</td>
  </tr>
</table>

## Networks List

id | network
-- | -------
1 | ChargePoint
2 | Blink
3 | SemaCharge
4 | GE WattStation
5 | Sun Country
6 | Circuit Electrique
7 | AddEnergie
8 | Tesla Supercharger
9 | AeroVironment
11 | OpenChargeMap
13 | RWE
14 | Oplaadpalen
15 | Endesa
16 | ESB
17 | Uppladdning
18 | Clever
19 | EVgo
22 | Lastestasjoner
23 | Enel Drive
25 | Volta
26 | Greenlots
34 | JNSH

# Photo

A location photo contributed by the PlugShare community.

## Object Properties

<table>
  <tr>
    <th>Property</th>
    <th>Description</th>
    <th>Can be null?</th>
  </tr>
  <tr>
    <td>
      <div class="field">id</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>Unique and immutable identifier</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">created_at</div>
      <div class="type">String (timestamp)</div>
    </td>
    <td>See Connector List</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">url</div>
      <div class="type">String (URL)</div>
    </td>
    <td>The URL to the full size photo</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">thumbnail</div>
      <div class="type">String (URL)</div>
    </td>
    <td>If available, link to a smaller version of the photo*</td>
    <td>Yes</td>
  </tr>
</table>

# Review (Check-In)

Check-Ins include:

* Tips for other drivers
* Reports of equipment problems
* Notification of time windows when a driver is going to be charging. This is helpful even when real-time status is available because it helps identify when a station is likely to become available.

## Object Properties

<table>
  <tr>
    <th>Property</th>
    <th>Description</th>
    <th>Can be null?</th>
  </tr>
  <tr>
    <td>
      <div class="field">id</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>Unique and immutable identifier</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">rating</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>1 = Positive / Success<br>0 = Neutral / Tips etc.<br>-1 = Trouble charging or other problem</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">created_at</div>
      <div class="type">String (timestamp)</div>
    </td>
    <td>When the Check-In was submitted</td>
    <td>No</td>
  </tr>
  <tr>
    <td>
      <div class="field">finished</div>
      <div class="type">String (timestamp)</div>
    </td>
    <td>The date the user plans to leave (or left) the location.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">comment</div>
      <div class="type">String</div>
    </td>
    <td>The driver’s comment</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">user</div>
      <div class="type">Object (User)</div>
    </td>
  <td></td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">volts</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>If average volts were recorded during this visit. Note: depending on vehicle capabilities or limitations, this may be lower than the station’s maximum capabilities.</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>
      <div class="field">amps</div>
      <div class="type">Number (integer)</div>
    </td>
    <td>If average amps were recorded during this visit. Note: depending on vehicle capabilities or limitations, this may be lower than the station’s maximum capabilities.</td>
    <td>Yes</td>
  </tr>
</table>

<!-- ## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

 -->