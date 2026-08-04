---
title: "Application Interface and Features"
date: 2026-08-03
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## NeonFoodMap

**NeonFoodMap** is a digital food and tourism discovery platform for **Vinh Khanh Food Street, District 4, Ho Chi Minh City**. Visitors can explore **points of interest (POIs) on a map and listen to audio guides** by location or QR code, follow **tour routes**, download data for offline use, and buy premium content. **Local partners** can register to create and manage profiles, POIs, audio, and QR codes; their registered content appears on the GPS map.

Visitors can explore and **listen to guides through the map or QR codes**. Paying users can unlock content and continue their experience offline, while partners update place content for visitors. Audio and interface languages can be changed in Settings. The app supports Vietnamese, English, Chinese, Japanese, and Korean.

## 1. Onboarding and location permission

On first use, the app shows the **“The Street Tells Its Story”** introduction and suggested stops in Vinh Khanh. The user allows **Precise Location**, then selects **Explore now** to open the map. Location permission lets the app find nearby POIs, navigate, and start guides when a visitor is close to a stop.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EOnboarding.jpg" alt="NeonFoodMap onboarding and location permission" >}}

## 2. Explore POIs on the map

The **Map** page is the main entry point for visitors. It shows the current location, nearby POI markers, zoom controls, and a recenter button. Users can search for food, places, or related content, then select a result or marker to open the POI details.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EMap.jpg" alt="NeonFoodMap map with nearby POIs" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EMapSearch.jpg" alt="Search for food places on the map" >}}

The POI details page shows a cover image, local story, distance, category, estimated duration, and address. It presents food and cultural information through an interactive map.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPOIDetail.jpg" alt="POI story details page" >}}

## 3. Audio guide for a POI

On the details page, the visitor selects **Start guide** to play audio for the current POI and selected language. The fixed player can play or pause, expand, skip back or forward 10 seconds, seek, and change playback speed. A guide can also start when the visitor enters a POI geofence or scans a valid QR code.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EAudioExpanded.jpg" alt="Expanded NeonFoodMap audio player" >}}

## 4. Scan QR codes and discover local partners

The QR button on the Map page opens **Scan a QR code at this place**. After the camera recognizes a POI QR code, the app opens the correct guide without requiring the visitor to search the map.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EQRScan.jpg" alt="Scan a QR code to open a POI guide" >}}

The POI page suggests related restaurants and places within walking distance. Selecting **QR Menu** opens the public partner profile, including its information, opening hours, price range, featured dishes, menu/QR code, and introduction audio.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerPublic.jpg" alt="Public partner profile and QR menu" >}}

## 5. Tour routes and reviews

The **Routes** page lets a visitor choose a tour. It then shows the route map, stop progress, current stop, next stop, and **Start tour** action. The visitor can open **Overview**, **Route** to see stops and locked points, and **Reviews** to read or submit a star rating and comment.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourChooser.jpg" alt="Tour selection dialog" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourJourney.jpg" alt="Active tour with current stop and progress" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourReview.jpg" alt="Tour review tab" >}}

During a tour, the user can change stops, open the route map, and play the guide for the current stop. If simulated location or GPS is off route, the app shows an alert so the user can locate themselves again.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourRoute.jpg" alt="Tour route stop list" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourRoute2.jpg" alt="Tour route stop list" >}}

## 6. Unlock premium tours and view invoices

Premium tours are locked until the user has access. The user selects **Unlock**, reviews the content summary, access period, and total payment, then continues through PayPal Sandbox. After the backend confirms the payment, the app shows a successful unlock. Invoices are available at **Me & Settings → Invoices & transaction history**.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourPremiumLocked.jpg" alt="Locked premium tour" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EInvoice.jpg" alt="NeonFoodMap transaction invoice" >}}

PayPal uses a Sandbox account and an external connection to confirm payment for a paid NeonFoodMap tour.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPaymentStart.jpg" alt="Confirm premium tour unlock and PayPal Sandbox payment" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPaymentSuccess.jpg" alt="Premium tour unlocked successfully" >}}

## 7. Download data and use the app offline

The **Offline Downloads** page provides data packages by area or tour. For an undownloaded package, the user selects **Download** while online. When it finishes, the app shows that it is downloaded, its storage use, and an option to remove it. Locked premium content sends the user to its tour to buy access.

Without a network, the map uses data saved on the device and shows an **Offline mode** label. Pending logs can sync again when the device reconnects.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EOfflinePackages.jpg" alt="NeonFoodMap offline data packages" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EOfflineReady.jpg" alt="Downloaded offline package ready for use" >}}

## 8. Account, language, and device

The **Me & Settings** page lets visitors choose a voice region, change interface language, view device and connection information, view invoices, and open the partner portal. Visitors can link a device to an email or select **Sign in with another account** to open authentication. The authentication page supports sign-in, account creation, and continuing as a guest.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ESettings.jpg" alt="Voice, language, and device data settings" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EAuth.jpg" alt="NeonFoodMap sign-in or account-creation screen" >}}

## 9. Partner portal

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerLogin.jpg" alt="Partner administration portal sign-in" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerSignup.jpg" alt="Partner administration portal registration" >}}

1. **Profile and audio:** update the venue name, opening hours, address, and price range; create or upload audio and submit it for review.

   {{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerProfile.jpg" alt="Venue profile and Audio Studio in the partner portal" >}}

2. **POIs and dishes:** create or update POIs, select their map location, and assign categories.

   {{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerPOI.jpg" alt="Create or update a partner POI on the map" >}}

3. **QR codes and distribution:** create or refresh expiring QR codes to print at the venue and direct visitors to the correct content.

   {{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerQR.jpg" alt="Create QR codes and distribute partner content" >}}

4. **Language changes:** partners can create POI information and change voice and display language. The system provides the five supported languages: Vietnamese, English, Chinese, Japanese, and Korean.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerAnalytics.jpg" alt="Language and voice settings" >}}

## Summary

**NeonFoodMap** provides digital food and tourism discovery for Vinh Khanh Street. Visitors can view POIs on a map, scan QR codes, listen to guides, take tours, unlock premium content, download offline data, and customize language and voice. Local partners can manage their profile, introduction content, POIs, audio, and QR codes.

The project integrates Amazon S3 and CloudFront to deliver the user interface; Amazon ECS Fargate and Amazon ECR for container deployment; Application Load Balancer for routing; Amazon RDS MySQL for data; Amazon VPC, IAM, Secrets Manager, and security groups for infrastructure security; and CloudWatch, SNS, AWS Budgets, and Cost Anomaly Detection for operations and costs. CI/CD is automated with GitHub Actions, GitHub OIDC, and AWS STS.
