# Web3 Video Player

This project introduces a new *protocol* for distributed video on web3. It aims to provide a integrated video eco-system which supports:
a) Playing of DRM and non-DRM content (using a modified video-js player);
b) Sourcing media meta-data from distributed platforms (IPFS, Filecoin, Polkadot ...) as well as from proprietary sources, stored with the open-source Livetree protocol;
c) Saving viewing events to distributed platforms (IPFS, Filecoin, Polkadot ...) as well as to other proprietary stores;
d) Extensions for use with referral programmes, loyalty programmes, paywalls, royalty distribution.

The project has been developed for livetree (www.livetree.com) with sponsorship from filecoin (www.filecoin.io) and Microsoft (www.microsoft.com).

Further information can be founds here:


## Content Provision

The video-player content can be consumed in a number different ways. 

a) **Hosted DRM Content** To access DRM content, a host is required who can support two endpoints for the web3_video protocol:
	1) *GetItemDetails*. Returning a json object specifying the media item details and associated meta-data (full spec below);
	2) *GetDRMTokens*. Returning the DRM tokens required to access the data (currently PlayReady, Widevine and FairPlay tokens are supported).

	Content can be hosted by livetree, if required. Please contact admin@livetree.com for more details; or if you wish to learn how to host DRM content for distribution via the web3_video protocol.

b) **Self-Hosted DRM Content** If you wish to host your own content, you will generally need to build your own end-points, as above

c) **Non-DRM Content** Non-DRM content (either static or streamed) can be accessed by simple URL reference. livetree may be able to assist with cost-effective media storage if required. Please contact admin@livetree.com	

Note: It is intended that these server-side endpoints will utilise distributed storage technologies, however local storage is anticipated in the short term while those technologies mature.

## Usage

The video-player can be used in a number of ways:

a) URL reference (inc. query parameters);

b) iframe; [iframe functionality requires re-factoring the code to avoid passing the video object through the DOM/window. See TODO list below.]

c) code download.

For both URL reference and iframe, you can pass several parameters as a json options object (*?options={...}*):

a) *itemurl* URL specifying the GetItemDetails endpoint providing media item details and meta-data
b) *drmurl* URL specifying the GetDRMTokens endpoint providing the DRM tokens for the item
c) *linkurl* URL specifying a link to prompt content-viewers to follow if necessary
d) *mediaId* The id of the item to retrieve from the endpoint
e) *referId* A referral id to be used within a referral link
f) *embed* Boolean flag to display embed code (<iframe>)
g) *height* Height of player on screen
h) *width* Width of player on screen

All of the options normally available with videojs should also be valid.

Note: It is intended that a default itemurl, drmurl and linkurl and so on are set by whomever is hosting the web3 video player. These can be overridden within the options, if necessary. These would normally contain [mediaId] and [referId] parameters which would be set using the mediaId and referId values passed.

For test purposes, and as a working example, you can find a hosted version of the player at:

	https://livetreeuta.azurewebsites.net/web3-video-player/index.html
	
The content and endpoints consumed by this player are hosted by: https://www.livetree.com

Examples:

------ Simple Cases------

https://livetreeuta.azurewebsites.net/web3-video-player/index.html?mediaId=7736&referId=cbce65637&embed 
https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"mediaId": 7736, "referId": "cbce65637"}
https://livetreeuta.azurewebsites.net/web3-video-player/index.html?src=http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4
https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"src": "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4"}


------ Extended Cases------

https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"mediaId": 7736, "referId": "cbce65637", "linkurl": "www.mywebsite.com/referrals?myagent=[referId]}

https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"src": "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4", "type": "video/mp4", "referId": "12345ABC", "linkurl": "https:www.mysite.co.uk/ambassadors?ambassador=[referId]"}

https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"src": "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4", "type": "video/mp4", "linkurl": "https:www.mysite.co.uk/ambassadors?ambassador=12345ABC"}

https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"itemUrl": "https://livetreeuta.azurewebsites.net/api/Web3Extensions/GetItemDetailsById?mediaId=7736&branchReferrerUserName=cbce65637", "drmUrl": "https://livetreeuta.azurewebsites.net/api/Web3Extensions/GetDRMToken?", "linkUrl": "https://www.mywebsite.com/landingpage"}

https://livetreeuta.azurewebsites.net/web3-video-player/index.html?options={"itemUrl": "https://livetreeuta.azurewebsites.net/api/Web3Extensions/GetItemDetailsById?mediaId=[mediaId]&branchReferrerUserName=[referId]", "drmUrl": "https://livetreeuta.azurewebsites.net/api/Web3Extensions/GetDRMToken?", "linkUrl": "https://www.mywebsite.com/landingpage", "mediaId": 7736, "referId": "cbce65637"}

NOTE: The json parser expects field names to be encapsualted in *quotes*. The most common error is badly formatted json options.

Any correctly formatted urls can be consumed in simple wepages via an <iframe>. Examples:

<iframe src='https://www.livetree.com/web3/video-player?options={"mediaId": 7736, "referId": "cbce65637"}'></iframe>


The iframe code can be extracted at any point directly from the player itself by clicking on the bottom right section of the player window, and copying the text exposed.

## Build environment
This project was initially generated using Angular but has been converted to basic stand-alone javascript without a CLI framework (such as Angular or React) and without reference to any external modules (such as npm).

## Build Overview

The build works via 4 key scripts:
a) *web3-video-config.js* Config parameters to provide endpoints for media item protocol data and drm tokens. These can be amended for custom use as required.
b) *web3-video-events.js* Player events (create, start, stop and so on) are recorded to IPFS for later push to filecoin (or other web3 storage as required by your usage). 
c) *web3-video.js* Main 'workhorse' of the player. This is adpated from core videojs and a number of plugins (contrib-eme, overlay-hyperlink, contrib-quality-levels, http-source-selector, titleoverlay, embed). A special injector script creates a custom *initPlayer(id, options)* function which pulls these plugins together and initiates the enhanced video player.
d) *web3-video-source.jss* This accesses the media and drm token protocol endpoints. Custom sources can be introduced into the *getWeb3VideoProtocol* function as indicated, if useWeb3ApiEndpoints is set to false in config.

These scripts are injected into <head> in index.html, along with the necessary .css.

<head>
...
  <link href="scripts/web3-video.css" rel="stylesheet">

  <script src="scripts/web3-video-config.js"></script>
  <script src="scripts/web3-video-events.js"></script>
  <script src="scripts/web3-video.js"></script>
  <script src="scripts/web3-video-source.js"></script>
...
</head>

The web3 video element should be set in the body, with id "web3-video", and a small script is added after this which calls the initPlayer function to initialise.

<body>
  <div class="content" role="main">
    <video 
      id="web3-video"
      class="video-js vjs-big-play-centered"
      controls
      preload="auto"
      width="800"
      height="600">
    </video>
  </div>
  <script src="scripts/web3-video-init.js"></script>
</body>



The main config parameters can be set up-front:

//-------------------------------------------------------------------------------------
// Set useWeb3ApiEndpoints to *false* if supplying Media data via an alternate function - to be specified in getWeb3VideoProtocol function in web3-video-sources
//private web3BaseApiUrl = "https://fairweb.livetree.com/api/Web3Extensions/";
web3BaseApiUrl = "https://livetreeuta.azurewebsites.net/api/Web3Extensions/";
config_optionsDefaults = { ///add below items into this object
  useWeb3ApiEndpoints : true,
  web3VideoItemProtocolEndpoint : web3BaseApiUrl + "GetItemDetailsById?itemId=[mediaId]&branchReferrerUserName=[referId]",
  web3VideoDRMTokenEndpoint : web3BaseApiUrl + "GetDRMToken?mediaElementId=[elementId]&itemId=[mediaId]",
  web3VideoDRMCertificateUri : "https://www.livetree.com/fairplay.msbs",
  web3VideoItemProtocolStatsEndpoint : web3BaseApiUrl + "SaveStats",
  web3VideoDRMQueryString : "?mediaElementId=[elementId]&mediaId=[mediaId]",
  web3VideoEmbedQueryStringItem : "?mediaId=[mediaId]&referId=[referId]",
  web3VideoEmbedQueryStringSrc : "?src=[src]&type=[type]",
  videojsCTAOverlayButtonLink:  "https://www.livetree.com?branchReferrerUserName=[referId]",
  IPFSNode:  "https://ipfs.infura.io:5001/api/v0/add" }
//-------------------------------------------------------------------------------------
  
  
On initialisingm, the video player:
	a) Gets default options from config;
	b) Gets any option supplied in-line in html;
	c) Parses the URL for options supplied in Query Parameters;
	d) Gets the media item protocol using the parameters passed (if an itemUrl is specified)
	e) Gets the media DRM protocol using the parameters passed (if a drmUrl is specified)
	f) Initialise the video using these options.

The code can used as an example and modified to your requirements as necessary. Most of the parameters specified can be over-ridden by either in-line html or from Query Parameters passed in via URL.

Bespoke event save routines can be used to replace (or alongside) the *pushIPFS(data)* function.


## Media Item Endpoint Protocol

    // This expects to return data in the following structure, retreived from the endpoint provided in itemUrl
    // Strucure is extended to support future enhancements
    //
    //    mediaId = Id,
    //    MediaElementId = // will give you a shortcut to the MediaElementId with the video of type 999
    //    CoverPic = MediaDefaultLiveTree.FileUrl,
    //    TrailerUrl = Trailer?.FileUrl,

    //     MediaElements = arrray[] of MediaElements 
          //{
          //    MediaElementId: int,  
          //    MediaType: int,
                //Advertising = 100,
                //TrailerMediaType = 600,
                //AmsVideo = 999,
                //Video = 888,
                //Poster = 111,
                //SecondPosters = 112,
                //Vtt = 555
          //    MIMEType: string,
          //    IsAudio: boolean,
          //    IsVideo: boolean,
          //    CdnHlsV3Url: string,
          //    CdnSmoothStreamingUrl: string,
          //    CdnPegDashV4Url: string,
          //    CdnHlsV4Url: string,
          //    LicenseSmoothStreamingWidevineUrl: string,
          //    LicenseSmoothStreamingPlayreadyUrl: string,
          //    LicensePegDashV4WidevineUrl: string,
          //    LicensePegDashV4PlayreadyUrl: string,
          //    LicenseHlsV3FairPlayBaseLicenseAcquisitionUrl: string,
          //    LicenseHlsV4FairPlayBaseLicenseAcquisitionUrl: string,
          //    CdnCommonEncryptionKid: string,
          //    CdnCommonEncryptionCbcsKid: string,
          //    GeoCheckApprovedCountriesCommaSeparatedIsoCodes: string,
          //    GeoCheckRejectedCountriesCommaSeparatedIsoCodes: string,
          //    IsPublic: boolean,
          //    FileExtension: string
          //};

    //    Price: number,
    //    ItemForm : string,

    //    SeedTotalRaisedToDate: number,
    //    SeedTargetTotalRaiseAmount: number,

    //    PublishRewardType: string,
    //    PublishRewardAmount:  number,
    //    PublishRewardPercentage: number,
    //    PublishTypeDesc: string,
    //    PublishType: string,
    //    PublishTypeDonationAmount: number,
    //    PublishTypeDonationQuantity: number,
    //    PublishTypeDonationReserveAmount: number,
    //    PublishTypeFixedPriceBuyPrice: number,
    //    PublishTypeFixedPriceQuantity: number,
    //    PublishTypeFreeQuantity: number,
    //    PublishTypeSubscriptionBuyPrice: number,
    //    PublishTypeSubscriptionPeriodType: string,
    //    PublishTypeSubscriptionPeriodTypeOtherFrequency: string,

    //    Title : string,
    //    SubTitle : string,
    //    IMDBId : string,
    //    Actors : string, [can be comma separated]
    //    Genres : string, [can be comma separated]
    //    Description : string,
    //    KeyWords : string, [can be comma separated]
    //    Category : string
    //

## DRM Token Endpoint Protocol

    // This expects to return data in the following structure, retreived from the endpoint provided in itemUrl
    //
    // ClientIpAddress: string
    // TokenWidevine: string
    // TokenPlayReady: string
    // ErrorMessage: string (expecty null )
    // ErrorCode: int
    // UserDevice: string

## Planned Enhancements (TODO)

1.	Update the mechanisms to inject the videojs player for consumption in the Angular app, to avoid passing it via the window/DOM (as this prevents iframing the app). This may require the full re-factoring outline in item 4 below [Done: 04.04.2021]
2.	Enhance IPFS save json structure: specifically to chain (DAG) saved packets together for sequential retrieval. Will need a queue mechanism to wait for returned cid to supply to next packet.
3.	Enhance IPFS save function to push to filecoin.
4.	Convert angular module to run entirely as in-line code which can be injected into the <head> with other plugin scripts - removes dependency on Angular, and ideally also npm, or other 'bloatware' development environments. [Done: 04.04.2021]
5.	Enhance the embed iframe code generator to automatically push the code to the copy-paste buffer instead of requiring the user to manually copy.
6.	Update the .css so that the button text can be made visable. [Done: 02.04.2021]
7.	Add ability to play subtitles for DRM content (selectable from a list of vtt tracks provided). [Done: 02.04.2021]
8.	Add skip forward and backwards controls to allow jump 30s.
9.	Fade *More ...* button back in at end of reel.
10.	Add config file to DOM instead of in code header of Angular module. [Done: 03.04.2021]
11.	Review end-point interfaces specs (simplify on a single mediaId and remove the need for mediaElementId). Return DRM url and media-specific link url along with other media meta-data. Clean-up termiinology areound mediaId and mediaId.
12.	Add playlist functionality (or a form of carousel) so as to be able to retrieve and manage a 'stack' of media rather than a single item. An additional endpoint may be needed to supply a media stack. (We refer to these stacks as *communities* since the intention is that each media stack would have a common theme for an interest group, and meta data regarding the interest group would be maintained in addition to the media data.)
13.	Update json parsers to me more resilient to 'case' and field names without quotes.[Done: 02.04.2021]
14.	Review initialization process to see if InitPlayr can be triggered automatically without the need for a script in the <body> after the video element.

------------Standard ReadME below ----------------

## Development server

Build uses light-server for serving page during development. [https://www.npmjs.com/package/light-server]

 - npm install light-server
 - light-server -s .
 
Note: look at light-server help for guidance on watching files for changes and auto-reaload.

avigate to `http://http://0.0.0.0:4000/`. The app will automatically reload if you change any of the source files specified for light-server


## Code scaffolding

None.

## Build

No build is required. Code is already native js.

## Further help

For further help please contact admin@livetree.com.
