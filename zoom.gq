/**
 * Zoom Class for Zoom user and Zoom Webinar Class
 */
class Zoom {
  constructor(apiKey, secret) {
    this.token = encodeJWT(apiKey, secret);
  }
  /**
   * Calls Zoom API
   * @param {string} url
   * @param {string} method
   * @param {string} payload
   */
  callAPI(url, method = "GET", payload = "", muteHttpExceptions = false) {
    try {
      var requestOptions = {
        method: method,
        headers: {
          Authorization: "Bearer " + this.token,
          "content-type": "application/json",
        },

        payload: payload,
        redirect: "follow",
        muteHttpExceptions: muteHttpExceptions,
      };

      var response = UrlFetchApp.fetch(url, requestOptions);
      Logger.log(response);

      var json = response.getContentText();
      var responseCode = response.getResponseCode();
      if (json) {
        var jsonData = JSON.parse(json);

        return jsonData;
      } else if (responseCode == 204) {
        return true;
      } else {
        return false;
      }
    } catch (e) {
      Logger.log("Error In API Call on Line " + e.line + " Message: " + e);
      return false;
    }
  }
}

class ZoomUser extends Zoom {
  constructor(apiKey, secret, id = "", email = "", firstName = "", lastName = "", type = 2, group = "", status = "") {
    super(apiKey, secret);
    this.id = id;
    this.email = email;
    this.firstName = firstName;
    this.lastName = lastName;
    this.type = type;
    this.group = group;
    this.status = status;
  }
  /**
   * Create Zoom User
   */
  createUser() {
    var json = {
      action: "create",
      user_info: {
        email: this.email,
        type: this.type,
        first_name: this.firstName,
        last_name: this.lastName,
      },
    };
    var payload = JSON.stringify(json);
    var method = "POST";
    var url = "https://api.zoom.us/v2/users/";

    var response = this.callAPI(url, method, payload, true);
    if (response.hasOwnProperty("id")) {
      this.id = response.id;
      return [response.id, ""];
    } else {
      return [false, response.message];
    }
  }
  /**
   * Change password to string provided
   * @param {string} password
   */
  set setid(id) {
    this.id = id;
  }
  changePassword(password) {
    var method = "PUT";
    var json = {
      password: password,
    };
    var payload = JSON.stringify(json);
    var url = "https://api.zoom.us/v2/users/" + this.id;
    return this.callAPI(url, method, payload);
  }
  /**
   * Change license to the type
   * @param {int} type
   */
  updateLicense(type = 1) {
    var method = "PATCH";
    this.type = type;
    var json = {
      type: type,
    };
    var payload = JSON.stringify(json);
    var url = "https://api.zoom.us/v2/users/" + this.id;
    return this.callAPI(url, method, payload);
  }
  /**
   * Used to assign webinar license with 500 capacity to user
   */
  assignWebinarLicense() {
    var json = {
      feature: {
        meeting_capacity: 500,
        large_meeting: false,
        webinar: true,
        webinar_capacity: 500,
      },
    };
    var payload = JSON.stringify(json);
    var method = "PATCH";
    var url = "https://api.zoom.us/v2/users/" + this.id + "/settings";
    return this.callAPI(url, method, payload);
  }
  /**
   * Add user to a group
   */
  addToGroup(group) {
    var json = {
      members: [
        {
          id: this.id,
        },
      ],
    };
    this.group = group;
    var payload = JSON.stringify(json);
    var method = "POST";
    var url = "https://api.zoom.us/v2/group/" + group + "/members";
    return this.callAPI(url, method, payload);
  }
  /**
   * Get details of user if id is defined
   */
  get details() {
    if (this.id != "") {
      var url = "https://api.zoom.us/v2/users/" + this.id;
      return this.callAPI(url);
    }
    return false;
  }
  /**
   * Populate the user details as long as either the email or id is defined
   */
  findUserDetails() {
    var found = false;
    var pageNumber = 1;
    var pageCount = 1;
    var response, user;

    while (!found && pageNumber <= pageCount) {
      var url = "https://api.zoom.us/v2/users/?page_size=300&page_number=" + pageNumber;
      response = this.callAPI(url);
      if (response) {
        pageNumber++;
        pageCount = response.page_count;
        for (var i = 0; i < response.users.length; i++) {
          if ((this.id != "" && response.users[i].id.toString().toLowerCase() == this.id.toString().toLowerCase()) || (this.email != "" && response.users[i].email.toString().toLowerCase() == this.email.toString().toLowerCase())) {
            user = response.users[i];
            found = true;
            break;
          }
        }
        if (user) {
          this.id = user.id;
          this.email = user.email;
          this.type = user.type;
          this.firstName = user.first_name;
          this.lastName = user.last_name;
          this.status = user.status;
        }
      }
    }
    return false;
  }
  /**
   * Get all users returned in user variable
   */
  getAllUsers() {
    var pageNumber = 1;
    var pageCount = 1;
    var response;
    var users = [];
    while (pageNumber <= pageCount) {
      var url = "https://api.zoom.us/v2/users/?page_size=300&page_number=" + pageNumber;
      response = this.callAPI(url);
      if (response) {
        pageNumber++;
        pageCount = response.page_count;
        users = users.concat(response.users);
      }
    }
    return users;
  }
}

/**
 * Class for the Webinars extends Zoom
 */
class ZoomWebinar extends Zoom {
  constructor(apiKey, secret, webinarID) {
    super(apiKey, secret);
    this.webinarID = webinarID.toString();
  }

  /**
   * Gets object with the registrants
   */
  get registrants() {
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID + "/registrants?page_size=300";
    var response = this.callAPI(url);
    var count = 0;
    var seperator = "?";
    var responseNext;
    while (response.next_page_token && count < 5) {
      if (url.indexOf("?") != -1) {
        seperator = "&";
      }
      responseNext = this.callAPI(url + seperator + "next_page_token=" + response.next_page_token);
      responseNext.registrants = response.registrants.concat(responseNext.registrants);
      response = responseNext;
      count++;
    }
    return response;
  }
  /**
   * Gets participants of webinar that has already ran
   */
  get participants() {
    var url = "https://api.zoom.us/v2/report/webinars/" + this.webinarID + "/participants?page_size=300";
    return this.callAPI(url);
  }
  /**
   * Gets the current participants of the webinar
   */
  get currentParticipants() {
    var url = "https://api.zoom.us/v2/metrics/webinars/" + this.webinarID + "/participants?page_size=300";
    return this.callAPI(url);
  }
  /**
   * Gets list of all panelists
   */
  get panelists() {
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID + "/panelists?page_size=300";
    return this.callAPI(url);
  }
  /**
   * Returns details for webinar
   */
  get details() {
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID;
    return this.callAPI(url);
  }
  /**
   * Updates contact information of Webinar
   * @param {string} contactName
   * @param {string} contactEmail
   */
  updateContactInfo(contactName, contactEmail) {
    var json = {
      settings: {
        contact_name: contactName,
        contact_email: contactEmail,
      },
    };
    var payload = JSON.stringify(json);
    var method = "PATCH";
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID;
    return this.callAPI(url, method, payload);
  }

  newRegistration(firstName, lastName, userEmail, phoneNumber) {
    var json = {
      email: userEmail,
      first_name: firstName,
      last_name: lastName,
      phone: phoneNumber,
      custom_questions: [
        {
          title: "Will anyone be attending this with you?",
          value: "No, just me",
        },
      ],
    };

    var payload = JSON.stringify(json);
    var method = "POST";
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID + "/registrants";
    return this.callAPI(url, method, payload);
  }

  /**
   * Updates the post webinar survey url for the speicifc webinar
   * @param {string} postSurveyURL
   */
  updateSurveyURL(postSurveyURL) {
    var json = {
      settings: {
        post_webinar_survey: true,
        survey_url: postSurveyURL,
      },
    };
    var payload = JSON.stringify(json);
    var method = "PATCH";

    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID;
    return this.callAPI(url, method, payload);
  }
  /**
   * Adds advisor as a panelist. If multiple emails are listed separated by commas split them and add each
   * @param {string} advisorEmail
   */
  addPanelist(advisorEmail) {
    var participant, emails;
    var jsonObj = {
      panelists: [],
    };

    //IF IT IS A COMMA SEPERATED STRING THEN SPLIT IT AND TRIM

    emails = advisorEmail.split(",");

    for (var i = 0; i < emails.length; i++) {
      participant = {
        name: GQ.getUsernameFromEmail(emails[i].trim()),
        email: emails[i].trim(),
      };
      jsonObj.panelists.push(participant);
    }

    var payload = JSON.stringify(jsonObj);
    var method = "POST";
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID + "/panelists";

    return this.callAPI(url, method, payload);
  }

  addAltHost(advisorEmail) {
    var json = {
      settings: {
        alternative_hosts: advisorEmail,
        notify_registrants: "false",
      },
    };
    var payload = JSON.stringify(json);
    var method = "PATCH";
    var url = "https://api.zoom.us/v2/webinars/" + this.webinarID;

    return this.callAPI(url, method, payload);
  }
  /**
   * Get recording and recording password
   */
  get recording() {
    //Get Recording
    var url = "https://api.zoom.us/v2/meetings/" + this.webinarID + "/recordings";
    var response = this.callAPI(url);
    var returnString = response.share_url;
    //Get Recording Password
    url = "https://api.zoom.us/v2/meetings/" + this.webinarID + "/recordings/settings";
    var settingsResponse = this.callAPI(url);

    returnString = returnString + " Password: " + settingsResponse.password;
    return returnString;
  }
  /**
   * Remove password from recording
   */
  removeRecordingPassword() {
    //Set password to empty string to reset password
    var json = {
      password: "",
    };
    var payload = JSON.stringify(json);
    var method = "PATCH";

    var url = "https://api.zoom.us/v2/meetings/" + webinarID + "/recordings/settings";
    return this.callAPI(url, method, payload);
  }
  /**
   * Gets the participants and then removes any that contain a domain found in the ignoreList
   * @param {array} ignoreList
   */
  filteredAttendees(ignoreList) {
    var array = this.participants.participants;

    return this.filterPanelists(this.filterList(ignoreList, array));
  }
  filterList(ignoreList, participants) {
    var array = participants;
    var returnArray = [];
    var result = [];
    const map = new Map();
    for (const item of array) {
      if (!map.has(item.id)) {
        map.set(item.id, true); // set any value to Map
        result.push({
          id: item.id,
          user_email: item.user_email,
        });
      }
    }
    if (!result) {
      return false;
    }

    for (var x = 0; x < result.length; x++) {
      var currentParticipant = result[x];

      //if not in ignore list
      if (
        ignoreList.some(function (v) {
          return currentParticipant.user_email.indexOf(v) >= 0;
        })
      ) {
      } else {
        //notfound
        if (currentParticipant.user_email) {
          returnArray.push(currentParticipant);
        }
      }
    }
    return returnArray;
  }
  /**
   * Gets the number of current attendees to a live webinar that are unique.
   * Returns the number of Attendees
   */
  get currentAttendees() {
    var participants = this.currentParticipants.participants;
    return this.filterPanelists(participants).length;
  }
  filterPanelists(participants) {
    var returnArray = [];

    //GET NUMBER OF PARTICIPANTS

    if (!participants) return false;
    //GET PANELISTS
    var panelists = this.panelists.panelists;

    ///REMOVE ALL DUPLICATE PARTICIPANTS
    var result = [];
    const map = new Map();
    for (const item of participants) {
      if (!map.has(item.id)) {
        map.set(item.id, true); // set any value to Map
        result.push({
          id: item.id,
        });
      }
    }
    //REMOVE PANELISTS FROM THE PARTICPANT LIST
    if (result.length > 0) {
      for (var y = 0; y < result.length; y++) {
        var isParticipant = false;
        for (var x = 0; x < panelists.length; x++) {
          if (panelists[x].id == result[y].id) {
            isParticipant = true;
            break;
          }
        }
        if (!isParticipant) {
          returnArray.push(panelists[x]);
        }
      }
    }
    return returnArray;
  }
  getWebinarsList(userID, fullList = false, pageSize = "300") {
    var url = "https://api.zoom.us/v2/users/" + userID + "/webinars?page_size=" + pageSize;

    var response = this.callAPI(url);
    var count = 0;
    var seperator = "?";
    var responseNext;
    while (response.next_page_token && count < 5 && fullList) {
      if (url.indexOf("?") != -1) {
        seperator = "&";
      }
      responseNext = this.callAPI(url + seperator + "next_page_token=" + response.next_page_token);
      responseNext.webinars = response.webinars.concat(responseNext.webinars);
      response = responseNext;
      count++;
    }
    return response;
  }
}
/* globally accessible factory method to create webinar */
function createZoomWebinar(apiKey, secret, webinarID) {
  return new ZoomWebinar(apiKey, secret, webinarID);
}
/* globally accessible factory method */
function createZoom(apiKey, secret) {
  return new Zoom(apiKey, secret);
}

/* globally accessible factory method */
function createZoomUser(apiKey, secret, id, email, firstName, lastName, type, group) {
  return new ZoomUser(apiKey, secret, id, email, firstName, lastName, type, group);
}
////Function to generate JWT Token for Zoom
var encodeJWT = function (apiKey, secret) {
  var header = JSON.stringify({
    typ: "JWT",
    alg: "HS256",
  });
  var encodedHeader = base64Encode(header);
  var iat = new Date().getTime() / 1000;
  var exp = new Date().getTime() + 86400;
  var payload = JSON.stringify({
    iss: apiKey,
    exp: exp,
  });
  var encodedPayload = base64Encode(payload);
  var toSign = [encodedHeader, encodedPayload].join(".");
  var signature = Utilities.computeHmacSha256Signature(toSign, secret);
  var encodedSignature = base64Encode(signature);
  console.log("Encoded Signature: " + encodedSignature);
  return [toSign, encodedSignature].join(".");
};

var base64Encode = function (str) {
  var encoded = Utilities.base64EncodeWebSafe(str);
  // Remove padding
  return encoded.replace(/=+$/, "");
};

/**
 * CAPITALIZE A STRING AND RETURN STRING WITH FIRST LETTER OF EACH WORK CAPITALIZED
 * @param phrase
 * @returns {GoogleAppsScript.Spreadsheet.OverGridImage | GoogleAppsScript.Slides.Image | void | string | *}
 */
function capitalizePhrase(phrase) {
  var reg = /\b(\w)/g;

  function replace(firstLetters) {
    return firstLetters.toUpperCase();
  }

  capitalized = phrase.replace(reg, replace);
  return capitalized;
}

/**
 * WILL CONVERT THE PART BEFORE THE @ SIGN TO A NAME REMOVING ANY NUMBERS AND REPLACING ANY DOTS WITH SPACES
 * GOAL IS TO TRY AND GET THE FULL NAME FROM AN EMAIL ADDRESS, IF UNABLE TO GET IT THEN IT WILL RETURN PANELIST
 * @param email
 * @returns Name
 */
function getUsernameFromEmail(email) {
  try {
    var regExp = new RegExp("(.*)(?:@.*)", "gi"); // "i" is for case insensitive
    var userName = regExp.exec(email)[1];

    var exp = RegExp("[.]", "gi");
    userName = userName.replace(exp, " ");
    var exp = RegExp("[^A-Za-z]", "gi");
    userName = userName.replace(exp, " ");
    return capitalizePhrase(userName);
  } catch (e) {
    return "Panelist";
  }
}
