## Selenium Server Settings

If the Selenium Server is used, then the connection related settings should be placed under the `"selenium""`. If both `webdriver` and `selenium` dictionaries are present, the `selenium` options will be merged with the `webdriver` ones. 

The `"selenium"` settings should also be used when configuring connections to cloud-based testing providers, such as [Browserstack][1], [SauceLabs][2] or [CrossBrowserTesting][3]. 

<table class="table table-bordered table-striped">
<thead>
 <tr>
   <th style="width: 100px;">Name</th>
   <th style="width: 100px;">type</th>
   <th style="width: 50px;">default</th>
   <th>description</th>
 </tr>
</thead>
<tbody>
 <tr>
   <td>`start_process`</td>
   <td>boolean</td>
   <td>false</td>
   <td>Whether or not to manage the Selenium process automatically.</td>
 </tr>
 
 <tr>
   <td>`server_path`</td>
   <td>string</td>
   <td>none</td>
   <td>The location of the Selenium <code>jar</code> file. This needs to be specified if <code>start_process</code> is enabled.<br>E.g.: <code>bin/selenium-server-standalone-2.43.0.jar</code></td>
 </tr>
 
 <tr>
   <td>`log_path`</td>
   <td>string|boolean</td>
   <td>none</td>
   <td>The location where the Selenium <code>output.log</code> file will be placed. Defaults to current directory.<br>To disable Selenium logging, set this to <code>false</code></td>
 </tr>
 
 <tr>
   <td>`version2`</td>
   <td>boolean</td>
   <td>false</td>
   <td>Set this to `true` if you need to use legacy Selenium Server 2.</td>
 </tr>
 
 <tr>
   <td>`port`</td>
   <td>integer</td>
   <td>4444</td>
   <td>The port number Selenium will listen on and/or Nighwatch will attempt to connect to.</td>
 </tr>
 
 <tr>
   <td>`cli_args`</td>
   <td>object</td>
   <td>none</td>
   <td>List of cli arguments to be passed to the Selenium process. Here you can set various options for browser drivers, such as:<br><br>
     <ul>
       <li>
         <code>webdriver.firefox.profile</code>: Selenium will by default create a new Firefox profile for each session. If you wish to use an existing Firefox profile you can specify its name here.<br>
         Complete list of Firefox Driver arguments available <a href="https://github.com/SeleniumHQ/selenium/wiki/FirefoxDriver" target="_blank">here</a>.
       </li>
       <li>
         <code>webdriver.chrome.driver</code>: Nightwatch can run the tests using <strong>Chrome</strong> browser also. To enable this you have to download the <a href="http://chromedriver.storage.googleapis.com/index.html" target="_blank">ChromeDriver binary</a> and specify it's location here.
     Also don't forget to specify chrome as the browser name in the <code>desiredCapabilities</code> object.<br>
     More information can be found on the <a href="https://sites.google.com/a/chromium.org/chromedriver/" target="_blank">ChromeDriver website</a>.<br>
       </li>
       <li>
         <code>webdriver.ie.driver</code>:
         Nightwatch works with <strong>Internet Explorer</strong> also. To enable this you have to download the <a href=
         "https://github.com/SeleniumHQ/selenium/wiki/InternetExplorerDriver" target="_blank">IE Driver binary</a> and specify it's location here.<br><br>
         Alternatively you can install the package [`iedriver`](https://www.npmjs.com/package/iedriver) from NPM.<br><br>
     Also you need to specify "internet explorer" as the browser name in the <code>desiredCapabilities</code> object.
       </li>
     </ul>
   </td>
 </tr>
 </tbody>
</table>

### Selenium Example Configuration

Here's an example configuration as part of the `nightwatch.conf.js` which uses a local Selenium Server with support for Firefox, Chrome, and Internet Explorer. 

The following **NPM** packages are assumed to be installed in the current project:

- [geckodriver][4] 
- [chromedriver][5]
- [selenium-server][6]
- [iedriver][7]

<div class="sample-test">
<pre><code class="language-javascript">module.exports = {
  src_folders: [],
  
  test_settings: {
    default: {
      launch_url: 'https://nightwatchjs.org'
    },
    
    selenium: {
      // Selenium Server is running locally and is managed by Nightwatch
      selenium: {
        start_process: true,
        port: 4444,
        server_path: require('selenium-server').path,
        cli_args: {
          'webdriver.gecko.driver': require('geckodriver').path,
          'webdriver.chrome.driver': require('chromedriver').path,
          'webdriver.ie.driver': process.platform === 'win32' ? require('iedriver').path : ''
        }
      },
      webdriver: {
        start_process: false
      }
    },
    
    'selenium.chrome': {
      extends: 'selenium',
      desiredCapabilities: {
        browserName: 'chrome',
        chromeOptions : {
          w3c: false
        }
      }
    },

    'selenium.firefox': {
      extends: 'selenium',
      desiredCapabilities: {
        browserName: 'firefox'
      }
    },
    
    'selenium.ie': {
      extends: 'selenium',
      desiredCapabilities: {
        browserName: 'internet explorer'
      }
    }
  }
}</code></pre></div>

### Browserstack Example Configuration

[Browserstack][8] is one of the most popular cloud testing platforms. Using it with Nightwatch is very straightforward and there is configuration in the auto-generated `nightwatch.conf.js` file.

Once you have an account, you need to set the following environment variables. [Dotenv][9] files are also supported by Nightwatch.
- `BROWSERSTACK_USER`
- `BROWSERSTACK_KEY`  

Remember to also enable HTTP keepalive for improved network performance.

<div class="sample-test">
<pre><code class="language-javascript">module.exports = {
  src_folders: [],
  
  webdriver: {
    keep_alive: true
  }
    
  test_settings: {
    default: {
      launch_url: 'https://nightwatchjs.org'
    },
    
    browserstack: {
      selenium: {
        host: 'hub-cloud.browserstack.com',
        port: 443
      },

      // More info on configuring capabilities can be found on:
      // https://www.browserstack.com/automate/capabilities?tag=selenium-4
      desiredCapabilities: {
        'bstack:options' : {
          local: 'false',
          userName: '${BROWSERSTACK_USER}',
          accessKey: '${BROWSERSTACK_KEY}',
        }
      }
    },
    
    browserstack.chrome': {
      extends: 'browserstack',
      desiredCapabilities: {
        browserName: 'chrome',
        chromeOptions : {
          w3c: false
        }
      }
    },

    'browserstack.firefox': {
      extends: 'browserstack',
      desiredCapabilities: {
        browserName: 'firefox'
      }
    },

    'browserstack.ie': {
      extends: 'browserstack',
      desiredCapabilities: {
        browserName: 'IE',
        browserVersion: '11.0',
        'bstack:options' : {
          os: 'Windows',
          osVersion: '10',
          local: 'false',
          seleniumVersion: '3.5.2',
          resolution: '1366x768'
        }
      }
    }
  }
}</code></pre></div>

[1]:	https://browserstack.com
[2]:	https://saucelabs.com/
[3]:	https://crossbrowsertesting.com/
[4]:	https://www.npmjs.com/package/geckodriver
[5]:	https://www.npmjs.com/package/chromedriver
[6]:	https://www.npmjs.com/package/selenium-server
[7]:	https://www.npmjs.com/package/iedriver
[8]:	https://browserstack.com
[9]:	https://www.npmjs.com/package/dotenv