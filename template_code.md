```javascript
// The first two lines are optional, use if you want to enable logging
const log = require('logToConsole');
log('data =', data);
const setDefaultConsentState = require('setDefaultConsentState');
const updateConsentState = require('updateConsentState');
const getCookieValues = require('getCookieValues');
const callInWindow = require('callInWindow');
const gtagSet = require('gtagSet');
const COOKIE_NAME = '<insert_cookie_name>';
/**
 * Splits the input string using comma as a delimiter, returning an array of
 * strings
 */
const splitInput = (input) => {
  return input.split(',')
      .map(entry => entry.trim())
      .filter(entry => entry.length !== 0);
};
/**
 * Processes a row of input from the default settings table, returning an object
 * which can be passed as an argument to setDefaultConsentState
 */
const parseCommandData = (settings) => {
  const regions = splitInput(settings['region']);
  const granted = splitInput(settings['granted']);
  const denied = splitInput(settings['denied']);
  const commandData = {};
  if (regions.length > 0) {
    commandData.region = regions;
  }
  granted.forEach(entry => {
    commandData[entry] = 'granted';
  });
  denied.forEach(entry => {
    commandData[entry] = 'denied';
  });
  return commandData;
};
// Parse consent settings to transform from list to object.
function parseSettings (x) {
  const permissions = x.toString().split(',').map(function (x) {return x.split(':')[1];});
  return {
    ad_storage: permissions[0] ? "granted" : "denied",
    analytics_storage: permissions[1] ? "granted" : "denied",
    functionality_storage: permissions[2] ? "granted" : "denied",
    personalization_storage: permissions[3] ? "granted" : "denied",
    security_storage: permissions[4] ? "granted" : "gdenied",
  };
}
/**
 * Called when consent changes. Assumes that consent object contains keys which
 * directly correspond to Google consent types.
 */
const onUserConsent = (consent) => {
  log(typeof consent);
  const consentModeStates = parseSettings (consent);
  updateConsentState(consentModeStates);
};
/**
 * Executes the default command, sets the developer ID, and sets up the consent
 * update callback
 * 
 * Note: Developer IDs are only required when you're building an implementation
 * that will be used across multiple websites by unrelated companies or
 * entities. If the implementation will be used by one site or entity,
 * please do not apply for a developer ID.
 */
const main = (data) => {
  // Set developer ID
  // gtagSet('developer_id.<replace_with_your_developer_id>', true);
  // Check if cookie is set and has values that corresopnd to Google consent
  // types. If it does, run onUserConsent().
  const userSettings = getCookieValues(COOKIE_NAME);
  if (userSettings !== 'undefined') {
    log('made it');
    const settings = parseSettings (userSettings);
    log('user:', settings);
    updateConsentState(settings);
  } else {
    log('oops');
    // Otherwise, set default consent state(s)
    data.defaultSettings.forEach(settings => {
    const defaultData = parseCommandData(settings);
    defaultData.wait_for_update = 500;
    setDefaultConsentState(defaultData);
    });
  }
  gtagSet('ads_data_redaction', data.ads_data_redaction);

  /**
   * Add event listener to trigger update when consent changes
   *
   * References an external method on the window object which accepts a
   * function as an argument. If you do not have such a method, you will need
   * to create one before continuing. This method should add the function
   * that is passed as an argument as a callback for an event emitted when
   * the user updates their consent. The callback should be called with an
   * object containing fields that correspond to the five built-in Google
   * consent types.
   */
  callInWindow('addConsentListenerExample', onUserConsent);
};
main(data);
data.gtmOnSuccess();
```
