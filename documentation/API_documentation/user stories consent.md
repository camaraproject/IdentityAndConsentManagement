# Consent Use-cases

This document captures Consent use-cases. The purpose of these UC is to provide material for API definition. Indeed, the (future) API capabilities must be able to handle these requirements


## Table of Contents

- [Consent Use-cases](#consent-use-cases)
  - [Table of Contents](#table-of-contents)
  - [1. User Story A: Contractual consent for Individual](#1-user-story-a-contractual-consent-for-individual)
  - [2. User Story B: One-time consent for individual](#2-user-story-b-one-time-consent-for-individual)
  - [3. User Story : Time-limited consent (opt-in) for individual](#3-user-story--time-limited-consent-opt-in-for-individual)
    - [3.1 C.1: individual user provides time-limited consent](#31-c1-individual-user-provides-time-limited-consent)
    - [3.2 C.2: user extends time-limited consent](#32-c2-user-extends-time-limited-consent)
    - [3.3 C.3: user revokes time-limited consent](#33-c3-user-revokes-time-limited-consent)
  - [4. User Story D: Opt-out consent for individual](#4-user-story-d-opt-out-consent-for-individual)
  - [5. User Story E: Enterprise track non-human related device (IoT)](#5-user-story-e-enterprise-track-non-human-related-device-iot)
  - [6. User Story F: Enterprise track human workforce related device (IoT)](#6-user-story-f-enterprise-track-human-workforce-related-device-iot)
  - [7. User Story G: Enterprise track human related device (IoT)](#7-user-story-g-enterprise-track-human-related-device-iot)



## 1. User Story A: Contractual consent for Individual
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a individual, I'm owner of a mobile line from a Telco A company . As part of my onboarding to a financial service provided by a Financial Enterprise FE, I authorize FE to get my last SIM Swap date from their application and during the course of my enrollment to FE services. This authorization is provided to the mobile line associated to the SIM card where this autorisation is provided. When checked, the line identifier will be obtained via number verify.  This authorization cannot be revoked during my entitlement. This authorization is valid as well in roaming situation |
| ***Roles, Actors and Scope*** | **Roles:** Customer:User of the mobile line<br> Telco: Telco operator owner of the SIM swap date information <br> **Actors:** Application service providers, aggregators, application developers. <br> **Scope:**  xx |
| ***Pre-conditions*** |The preconditions are listed below:<br><ol><li>The Customer:User has a mobile contract to Telco A.</li><li>Application service provided has onboarded via aggregators on SIM Swap API</li><li>The aggregator has onboarded to use Telco A APIs </li></ol>|
| ***Activities/Steps*** | **Starts when:** The customer onboard on a financial service application. <br>**then:** The application informs the customer of a required contractually consent to provided SIM swap information.<br>**then:** The customer provided this consent <br>**Ends when:** The consent is captured/stored in Telco.<br> |
| ***Post-conditions*** | From Telco consent portal, the customer is able to check & manage consent allowed  |
| ***Exceptions*** | Several exceptions might occur during the consent operations:<br><ul><li>Denied by Carrier: Any user/business condition and/or regulation that forbids the consent capture</li><li>Denied by Customer : customer stop application onboarding.</li></ul>|
| ***Variation  & question*** | This UC has 2 variants: <br><ul><li> With an aggregator </li><li> without an aggregator (directly between application and telco</li></ul>|


<br>
<br>

## 2. User Story B: One-time consent for individual
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a individual, I'm owner of a mobile line from a Telco A company . As part of my onboarding to a gaming service on application appG, I authorize appG to get my phone number from my network operator. I'm connected to my operator mobile network (not in roaming situation). This authorization is valid only for one access. |
| ***Roles, Actors and Scope*** | **Roles:** Customer:User of the mobile line<br> Telco: Telco operator owner of the SIM associated number information |
| ***Pre-conditions*** |The preconditions are listed below:<br><ol><li>The Customer:User has a mobile contract to Telco A.</li><li>Application service provided has onboarded on number Verify API from Telco A</li>|
| ***Activities/Steps*** |  |
| ***Post-conditions*** |   |
| ***Exceptions*** | |
| ***Variation  & question*** | |


<br>
<br>

## 3. User Story : Time-limited consent (opt-in) for individual

### 3.1 C.1: individual user provides time-limited consent
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a individual, I'm owner of a mobile line from a Telco A company . As part of my onboarding to a gaming service on application appG, I authorize appG to get my location from my network operator in my home country only (I do not authorise location tracking when I'm onbroad). I authorize the application to access my location for one month starting today.  |
| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** | The line customer is able to see this consent on my network operator consent portal.   |
| ***Exceptions*** | |
| ***Variation  & question*** | 

### 3.2 C.2: user extends time-limited consent
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a individual, I'm owner of a mobile line from a Telco A company . As part of my onboarding to a gaming service on application appG, I authorized appG to get my location from my network operator in my home country. I connect to my network operator consent portal (or to the app dashboard) to extend the authorization for one additional month. |
| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** |    |
| ***Exceptions*** | |
| ***Variation  & question*** | 

### 3.3 C.3: user revokes time-limited consent
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a individual, I'm owner of a mobile line from a Telco A company . As part of my termination to a gaming service on application appG, I connect to my network operator consent portal to revoke the authorization before planned termination date. |
| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** |    |
| ***Exceptions*** | |
| ***Variation  & question*** | 

<br>
<br>

## 4. User Story D: Opt-out consent for individual 
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a individual, I'm owner of a mobile line from a Telco A company . As part of my onboarding to a enhanced communication service on application appG, I authorize appCom to subscribe & get  notification about my roaming status (in order to limit application capabilities ). This consent is valid for one year and will be automatically rolled over if I did not opt-out. I want to be able to see this consent on my network operator consent portal.  |
| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** |    |
| ***Exceptions*** | |
| ***Variation  & question*** | 

<br>
<br>

## 5. User Story E: Enterprise track non-human related device (IoT)
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As an enteprise, I want to be able to track  location of a fleet of non-human-related devices  (like container). In order to do that I provided a list of IoT MSISDN. I should be able to manage this list by adding/removing device.  |
| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** |    |
| ***Exceptions*** | |
| ***Variation  & question*** | No consent required for this UC |

<br>
<br>

## 6. User Story F: Enterprise track human workforce related device (IoT)
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As an delivery service enterprise, I want to track my employee location in order to provide them delivery mission. My employees has to use my entreprise application and this application must be able to localise them. Usage of this enterprise app is mandatory and part of the work aggreement.
| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** |    |
| ***Exceptions*** | |
| ***Variation  & question*** | Do we need consent between Telco & human workforce? This agrrement is proably part of the work contract and don't need additional management - need confirmation.|

<br>
<br>

## 7. User Story G: Enterprise track human related device (IoT)
<br>

| **Item** | **Details** |
| ---- | ------- |
| ***Summary*** | As a car manufacturer, I want to be able to track worldwide location of my cars. As car manufacturer, I equip all cars with SIM Card. As car manufacturer, I offer assistance service to its customer based on location. |

| ***Roles, Actors and Scope*** |  |
| ***Pre-conditions*** | |
| ***Activities/Steps*** |  |
| ***Post-conditions*** |    |
| ***Exceptions*** | |
| ***Variation  & question*** | Do telco need to track car customer consent ? the consent is only between the car manufaturer and the customer?|