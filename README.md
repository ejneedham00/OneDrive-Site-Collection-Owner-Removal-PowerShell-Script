<h1>Remove a Site Collection Admin From Multiple OneDrive Sites</h1>

<h2>What it does</h2>

<p>This script is for removing one account from the site collection admin list on a batch of OneDrive sites. You give it a CSV of OneDrive URLs, and it goes through each one and removes the named account as an admin. Unlike many of my other scripts, this script is <b>NOT READ ONLY</b> and will actually make changes using a <code>SET</code> command.</p>

<h2>How it works</h2>

<ol>
  <li>Connects to SharePoint Online with <code>Connect-SPOService</code>, using the admin centre URL. <code>DOMAIN</code> needs replacing with your tenant name, and you'll get an interactive sign-in.</li>
  <li>Reads <code>C:\SiteCollectionOwnerRemovalList.csv</code> into <code>$users</code> with <code>Import-Csv</code>. The CSV needs a column headed <code>OneDriveURL</code>.</li>
  <li>Loops through each row and stores that row's OneDrive URL in <code>$OneDriveUrl</code>.</li>
  <li>Runs <code>Set-SPOUser</code> against that site to set <code>-IsSiteCollectionAdmin</code> to <code>$False</code> for <code>SiteCollectionOwner@domain.com</code>. This removes that account's site collection admin rights on the OneDrive.</li>
  <li>Moves on to the next row. Nothing is printed, so there is no summary or confirmation.</li>
</ol>

<h2>Prerequisites</h2>

<ul>
  <li>The SharePoint Online Management Shell module (<code>Microsoft.Online.SharePoint.PowerShell</code>)</li>
  <li>An account with the SharePoint Administrator or Global Administrator role</li>
  <li>A CSV at the path shown, with a <code>OneDriveURL</code> column containing full OneDrive URLs</li>
</ul>

<h2>Notes / Limitations</h2>

<ul>
  <li><b>The script is broken as written.</b> <code>-site '$OneDriveURL'</code> is in single quotes, so PowerShell passes the literal text <code>$OneDriveURL</code> rather than the URL from the CSV. Every iteration will fail with an error instead of touching a real site. The fix is to remove the quotes: <code>-Site $OneDriveUrl</code>.</li>
  <li><b>The comment doesn't match the code.</b> It says "Reset the storage quota", but nothing here touches quota. The command only removes a site collection admin.</li>
  <li><b>Placeholders need replacing.</b> <code>DOMAIN</code> in the admin URL and <code>SiteCollectionOwner@domain.com</code> are both placeholders. The account is hardcoded, so the same one is removed from every site in the CSV.</li>
  <li>It only removes the <b>site collection admin</b> flag. The account isn't removed from the site entirely, and it keeps any other permissions it has through groups or direct grants.</li>
  <li>It can't remove an account that is the primary owner of the site. It only works on accounts added as extra admins.</li>
  <li>There is no error handling or logging. If a URL is wrong or a site is missing, you'll only see red errors in the console, with no record of which rows succeeded.</li>
  <li>There is no confirmation or <code>-WhatIf</code> step, so test it on one or two sites first, and check the CSV for blank rows or stray spaces.</li>
  <li>A CSV with a different column name, such as <code>OneDriveUrl</code> or <code>URL</code>, makes <code>$user.OneDriveURL</code> come back empty. PowerShell's property matching ignores case, but not the name itself.</li>
</ul>
