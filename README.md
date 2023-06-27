# fixed-GTM-consent-tag-template
[Go here](https://developers.google.com/tag-platform/tag-manager/templates/consent-apis) to see Google's official documentation on how to set up this tag.
[Go here](https://emmadoesdata.com/an-easy-consent-tag-template-that-actually-works/) for a detailed explanation of why it doesn't work and you should use this version, instead.
## Assumptions
1. The cookie will always be a string formatted as such: `{adConsentGranted: '<insert_status>', analyticsConsentGranted: '<insert_status>', functionalityConsentGranted: '<insert_status>', personalizationConsentGranted: '<insert_status>', securityConsentGranted: '<insert-status>'}`.  If this needs to change, update the `parseSettings` function to reflect your preferred ordering.
2. You understand this tag will only allow you to control how consent status affects which of your GTM tags fire. Implementing this solution does not guarantee compliance with GDPR, CCPA, or any other privacy law.
3. You have enabled GTM's consent mode and configured the consent requirements for each tag.
## Instructions
### Setting Up the Template Fields
1. Create the new tag template in the appropriate GTM container.
2. Go to the Fields tab and add a new field.
3. Choose Param table as the field type and name it `defaultSettings`.
4. Expand the field and update the display name to `Default Settings`.
5. Add a Text input column named `region`. Check the box for `Require column values to be unique`.
6. Expand the region column and change the display name to `Region (leave blank to have consent apply to all regions)`.
7. Add another Text input column named `Granted`. Change its display name to `Granted Consent Types (comma separated)`.
8. Add one more Text input column named `Denied Consent Types (comma separated)`.
9. Add a Checkbox fielde named `ads_data_redaction`. Make its display name`Redact Ads Data`.
### Setting Up the Code
1. Navigate to the Code tab.
2. Copy and paste [this code](template_code.md).
3. Update the `COOKIE_NAME` constant's value so it matches the name of the cookie storing your consent data.
4. If you have a Google developer ID and want to utilize it, uncomment the line shown in the picture and insert your ID.
### Setting Up the Permissions
1. Go to the Permissions tab and click Accesses consent state.
2. Add each available consent type, and make sure to check the Write permissions.
3. Go to Reads cookie value. Select Specific and enter the name of your cookie.
4. Under Writes to data layer, add `ads_data_redaction`.
### Setting Up the Tag
1. Create a tag that uses your custom template.
2. Set it to trigger on Consent Initialization - All Pages.
3. In the tag's Settings table, enter a comma-separated list of all the consent types in their appropriate Denied/Granted columns. This will be the site's default consent state for any given region.
4. To make region-specific defaults, add a new row and put the region's name in the Region column and fill out the Granted/Denied columns as instructed in step 3.
## Special Note Regarding Regions
I haven't encountered a client who asked for region-specific policies. As such, this feature is not yet tested. Please submit an issue if you encounter any bugs.
