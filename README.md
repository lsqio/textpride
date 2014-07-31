textpride
=========

------------
### Android

#### Setting up Webview

1. Add permission to android app (in the manifest) to use internet:<br>
  ```
  <uses-permission android:name="android.permission.INTERNET" />
  ```

2. Create an activity (TextprideWebViewActivtity.class)
  - Activity Title String (strings.xml) (can use cusomt title what app provider desires):
  ```
  <resources>
      ...
      <string name=“textpride_webview_activity”>Textpride Content</string>
  </resources>
  ```
  - Declare it in the manifest
  ```
  <application ... >
    ...
    <activity
        android:name= {package}.”TextprideWebViewActivtity”
        android:label="@string/textpride_webview_activity"
            android:parentActivityName=“{parent activity name}” >
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value="com.example.myfirstapp.MainActivity" />
    </activity>
  </application>
  ```

3. Create a layout (named: “textpride_webview_activty”);
  - Add the WebView to the activity layout
  ```
  <WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
  />
  ```

4. Add the layout (created above) to the onCreate() method of the activity:<br>
  ```
  setContentView(R.layout.textpride_webview_activty);
  ```

5. Get the Url that will be provided by the intent object when activity is started (below):
  ```
  Intent intent = getIntent();
  String textprideUrl = intent.getStringExtra({ParentMainActivity}.TEXTPRIDE_URL);
  //ex: “ad.textpride.com/refId/urlId”
  String contentId = intent.getStringExtra({ParentMainActivity}.CONTENTID);
  String userId = intent.getStringExtra({ParentMainActivity}.USERID);
  ```

6. Get the WebView in the activity by id
  ```
  WebView textprideWebView = (WebView) findViewById(R.id.webview);
  ```
  - Setup the web view:
  ```
  WebSettings webSettings = textprideWebView.getSettings();
  webSettings.setJavaScriptEnabled(true);

  ```
  - Setup page redirect for callback (** this is where app provider content unlock function goes):
  ```
  textprideWebView.setWebViewClient(new WebViewClient() {  
    @Override  
    public void onPageStarted(WebView view, String url, Bitmap favicon){  
      if (url.startsWith(“textprideUnlockContent/time=number/complete=boolean")) {  
        	view.stopLoading();  
  	// in app sticker unlock function
  	// close web view
      }  
    }  
  }  
  ```

7. load the Url (with params for post):
  ```
  String url = textprideUrl;
  String postData = “appName=“{ }”&contentId=contentId&userId=userId;
  webview.postUrl(url,EncodingUtils.getBytes(postData, “BASE64”));
  ```
===========

#### Setting up Content to Unlock Screen
1. Generate Stickers (However the app provider chooses);

2. Create fields for intent to send parameters to the new activity
  ```
  public static String TEXTPRIDE_URL = “{package name}.TEXTPRIDEURL”;
	public static String CONTENT_ID = “{package name}.CONTENTID”;
	public static String USER_ID = “{package name}.USERID”;

  ```
3. Attach textpride Url to stickers
4. If stickers have text pride Url, add/show unlock button as seem fit
  - button must run a method, ex: `textprideViewContent(View view);`
    - therefor onclick of unlock button, run textprideViewContent(View view); 
  - method definition:
  ```
  textprideViewContent(View view){
    String textprideUrl = {url from client for webview, attached to the sticker};
    String stickerId = { the id of the sticker clicked on}
    String userId = {id of the user in the app}
    int code = 404;
    HttpURLConnection connection = null;
    try{         
        URL myurl = new URL(textprideUrl);        
        connection = (HttpURLConnection) myurl.openConnection();     
        connection.setRequestMethod("HEAD");         
        code = connection.getResponseCode();        
        System.out.println("" + code); 
    } catch {
    //Handle invalid URL
    }
    
    // if code = 200 (not 404 or 500)
    Intent viewTextPrideContent = new Intent(this, textprideWebViewActivtity.class);
    viewTextPrideContent.putExtra(TEXTPRIDE_URL, textprideUrl);
    viewTextPrideContent.putExtra(CONTENT_ID, stickerId);
    viewTextPrideContent.putExtra(USER_ID, userId);
    startActivity(viewTextPrideContent);
  }
  ```

