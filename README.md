<p align="center">
    <img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
    </p>
<h1><u>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</u></h1>
    <p>In this lab demonstration, I observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups, and get a basic sense of DNS and what it's doing.</p>
    <h2><em>Environments and Technologies Used</em></h2>
        <ul>
            <li>Microsoft Azure (Virtual Machines/Compute)</li>
            <li>Remote Desktop</li>
            <li>Various Command-Line Tools</li>
            <li>Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)</li>
            <li>Wireshark (Protocol Analyzer)</li>
        </ul>
    <h2><em>Operating Systems Used</em></h2>
        <ul>
            <li>Windows 10 (21H2)</li>
            <li>Ubuntu Server 20.04</li>
        </ul>
    <h2><em>High-Level Lab Overview</em></h2>
        <ul>
            <li>A-Record Exercise</li>
            <li>Local DNS Cache Exercise</li>
            <li>CNAME Record Exercise</li>
            <li>Create Some Sample File Shares With Various Permissions</li>
            <li>Attempt To Access File Shares As A Normal User</li>
            <li>Create an "ACCOUNTANTS" Security Group, Assign Permissions, and Test Access.</li>
        </ul>    
    <h2><u>Actions and Observations</u></h2>
    <h2><strong>- A-Record Exercise</strong></h2>
        <h3><u>Overview</u></h3>
                <ul>
                    <li>Inspect DNS A-Records on the Server (Hostname to IP Address Mappings)</li>
                    <li>Create Some of Our Own A-Records on the Server and Observe Them From the Client.</li>
                    <li>Delete Records From Server and Inspect/Clear the Client DNS Cache to Gain Understanding.</li>
                    <li>Touch on "CNAME" Records (Mapping One Name To Another Name)</li>
                </ul>
        <h3>- Step 1: Connect/Log into the Domain Controller as an Admin.</h3>
            <img src="https://i.imgur.com/JaBRoJH.png" height="20%" width="20%"/>
        <br>
            <img src="https://i.imgur.com/m4RGH4n.png" height="20%" width="20%"/>
        <br>
            <img src="https://i.imgur.com/ZajHcZf.png" height="50%" width="50%"/>



        <h3>- Step 2: Connect/Log into Client-1 as an Admin.</h3>
                <img src="https://i.imgur.com/saHRhLs.png" height="20%" width="20%"/>
        <br>
                <img src="https://i.imgur.com/Z2gTQMF.png" height="20%" width="20%"/>
        <br>
                <img src="https://i.imgur.com/9JrIxvy.png" height="50%" width="50%"/>
        <h3>- Step 3: From Client-1 We Try to Ping "mainframe" and We Notice That it Fails.</h3>
                <img src="https://i.imgur.com/Hd04XD6.png" height="50%" width="50%"/>
        <h3>- Step 4: Nslookup "mainframe" and Notice That it Fails. (No DNS Record)</h3>
                <img src="https://i.imgur.com/9Nf7At7.png" height="50%" width="50%"/>
        <h3>- Step 5: Create a DNS A-Record on DC-1 for "mainframe" and Have it Point to DC-1's Private IP Address.</h3>
            <p>- For this step, we go into Server Manager on DC-1, Go Under Tools in the Menu Bar, and Click DNS.</p>
                <img src="https://i.imgur.com/ptmxLcI.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/h8JypbF.png" height="50%" width="50%"/>
            <p>- From here, We Click On DC-1, Then Forward Lookup Zones, and Then Our Domain, freeyourmind.com, to See a List of Our Current A-Records.</p>
                <img src="https://i.imgur.com/b402nBM.png" height="50%" width="50%"/>
            <p>- Next, We'll Add Our Own A-Record Named "mainframe".</p>
                <img src="https://i.imgur.com/9PZitVy.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/9TDJCHp.png" height="50%" width="50%"/>
            <p>- We set the IP Address to the Same IP as DC-1, Just to Ensure it Pings Successfully. (Completely Random, Just For Testing and Observation Purposes)</p>
                <img src="https://i.imgur.com/8P0v8UD.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/fuUaXOD.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/fSHdXHI.png" height  ="50%" width="50%"/>
        <h3>- Step 6: Now, If We Go Back to Client-1 and Try to Ping it, We Can Observe That it Works.</h3>
                <img src="https://i.imgur.com/asy813n.png" height="50%" width="50%"/>
    <h2><strong>- Local DNS Cache Exercise</strong></h2>
        <h3><u>Overview</u></h3>
                <ul>
                    <li>Go Back To DC-1 and Change "mainframe's" record address to 8.8.8.8</li>
                    <li>Go Back To Client-1 and Ping "mainframe" Again. Observe That It Still Pings the Old Address.</li>
                    <li>Observe the Local DNS Cache (ipconfig /displaydns)</li>
                    <li>Flush the DNS Cache (ipconfig /flushdns). Observe That the Cache is Empty.</li>
                    <li>Attempt to Ping "mainframe" Again. Observe the Address of the New Record is Showing Up.</li>
                </ul>
        <h3>- Step 1: Go Back To DC-1 and Change "mainframe's" Record Address to 8.8.8.8</h3>
                <p>- First, We'll Go Back to DC-1, go to Our "mainframe" A-Record, and Update Our Address to 8.8.8.8.</p>
                    <img src="https://i.imgur.com/cDRjYmo.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/I25HWcX.png" height="50%" width="50%"/>
                <p>- Now we Can See That Our "mainframe" A-Record has an Address of 8.8.8.8.</p>
                    <img src="https://i.imgur.com/cdU77VM.png" height="50%" width="50%"/>
        <h3>- Step 2: Go Back To Client-1 and Ping "mainframe" Again. Observe That It Still Pings the Old Address.</h3>
                <img src="https://i.imgur.com/vvm3aoD.png" height="50%" width="50%"/>
            <p>- We Notice That It's Still Pinging the Old Address, and That is Because This Address Still Exists in the Local DNS Cache.</p>
        <h3>- Step 3: Observe the Local DNS Cache (ipconfig /displaydns).</h3>
                <img src="https://i.imgur.com/LUAvx19.png" height="50%" width="50%"/>
                <p>To Ensure the DNS updates, We'll Want To Ensure That We Flush The Cache.</p>
        <h3>- Step 4: Flush the DNS Cache (ipconfig /flushdns). Observe That the Cache is Empty.</h3>
                <p>- For This Step, First We'll Use the Command "ipconfig /flushdns" to Flush the DNS Cache.</p>
                    <img src="https://i.imgur.com/DbvRb4G.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/grPT2rM.png" height="50%" width="50%"/>
                <p>- Lastly For This Step, We'll Observe To Ensure That The DNS Record Was Updated For "mainframe".</p>
                    <img src="https://i.imgur.com/Ucs5pQT.png" height="50%" width="50%"/>
        <h3>- Step 5: Attempt to Ping "mainframe" Again. Observe the Address of the New Record is Showing Up.</h3>
                <img src="https://i.imgur.com/yqry6ej.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/xRdjOBL.png" height="50%" width="50%"/>
    <h2><strong>- CNAME Record Exercise</strong></h2>
        <h3><u>Overview</u></h3>
                <ul>
                    <li>Go Back to DC-1 and Create a CNAME Record That Points the Host “Search” to “www.Google.com”.</li>
                    <li>Go Back to Client-1 and Attempt to Ping “Search”, Observe the Results of the CNAME Record.</li>
                    <li>On Client-1, nslookup "search", Observe the Results of the CNAME Record.</li>
               </ul>
            <h3>- Step 1: Go Back to DC-1 and Create a CNAME Record That Points the Host “Search” to “www.Google.com”</h3>
                <img src="https://i.imgur.com/2utKyy5.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/4kIzaOT.png" height="50%" width="50%"/>
            <h3>- Step 2: Go Back to Client-1 and Attempt to Ping “Search”, Observe the Results of the CNAME Record.</h3>
               <img src="https://i.imgur.com/uhD1vQa.png" height="50%" width="50%"/>
               <img src="https://i.imgur.com/NnLGQKj.png" height="50%" width="50%"/>  
    <h2><strong>- Create Some Sample File Shares With Various Permissions</strong></h2>
        <h3><u>Overview</u></h3>
               <ul>
                    <li>Connect/Log Into DC-1 as Our Domain Admin Account (freeyourmind.com\trinity_admin).</li>
                    <li>Connect/Log Into Client-1 as a Normal User (freeyourmind.com/feco.fona)</li>
                    <li>On DC-1, On The C:\Drive, Create 4 Folders: "read-access", "write-access", "no-access", "accounting".</li>
                    <li>Set the Following Permissions (Share the Folder) For the "Domain Users" Group.</li>
                    <li>Folder: "read-access", Group: "Domain Users", Permissions: "Read".</li>
                    <li>Folder: "write-access", Group: "Domain Users", Permissions: "Read/Write".</li>
                    <li>Folder: "no-access", Group: "Domain Admins", Permissions: "Read/Write".</li>
               </ul>
            <h3>- Step 1: Connect/Log Into DC-1 as Our Domain Admin Account (freeyourmind.com\trinity_admin).</h3>
                <img src="https://i.imgur.com/ahG9HVy.png" height="50%" width="50%"/>
            <h3>- Step 2: Connect/Log Into Client-1 as a Normal User (freeyourmind.com/feco.fona)[This is a random user that we took from our employees list in active directory that we created in the previous lab.]</h3>
                <img src="https://i.imgur.com/a0IPG1P.png" height="50%" width="50%"/>
            <br>
                <img src="https://i.imgur.com/O87BdCZ.png" height="20%" width="10%"/>
            <br>
                <img src="https://i.imgur.com/ONBr594.png" height="50%" width="50%"/>
            <h3>- Step 3: On DC-1, On The C:\Drive, Create 4 Folders: "read-access", "write-access", "no-access", "accounting".</h3>
                <img src="https://i.imgur.com/mFnIJNh.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/r7cLozj.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/aJ2hFzz.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/PFkwrEb.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/hYsA5d1.png" height="50%" width="50%"/>
            <h3>- Step 4: Set the Following Permissions (Share the Folder) For the "Domain Users" Group.</h3>
                <h4>- Folder: "read-access", Group: "Domain Users", Permissions: "Read".</h4>
                    <img src="https://i.imgur.com/CuTeeni.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/EpS8gKN.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/twXr3RX.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/MlxzeAf.png" height="50%" width="50%"/>
                <h4>- Folder: "write-access", Group: "Domain Users", Permissions: "Read/Write".</h4>
                    <img src="https://i.imgur.com/6fuTwvv.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/6ZhKkAp.pngv" height="50%" width="50%"/>
                <h4>- Folder: "no-access", Group: "Domain Admins", Permissions: "Read/Write".</h3>
                    <img src="https://i.imgur.com/JOWF9Qh.png" height="50%" width="50%"/>
                    <img src="https://i.imgur.com/aqDreWI.png" height="50%" width="50%"/>
    <h2><strong>- Attempt To Access File Shares As a Normal User</strong></h2>
        <h3><u>Overview</u></h3>
               <ul>
                    <li>On Client-1, Navigate to the Shared Folder (Start, Run, \\DC-1).</li>
                    <li>Try to Access the Folders You Just Created.</li>
                </ul>
            <h3>- Step 1: On Client-1, Navigate to the Shared Folder (Start, Run, \\DC-1).</h3>
                <img src="https://i.imgur.com/fdhfbGN.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/tx22sLF.png" height="50%" width="50%"/>
            <h3>- Step 2: Try to Access the Folders You Just Created.</h3>
                <img src="https://i.imgur.com/IRtR0NT.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/FfmzxN7.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/UBTKA2Q.png" height="50%" width="50%"/>
    <h2><strong>- Create an "ACCOUNTANTS" Security Group, Assign Permissions, and Test Access.</strong></h2>
        <h3><u>Overview</u></h3>
                <ul>
                    <li>Go Back to DC-1, in Active Directory, Create a Security Group Called "ACCOUNTANTS".</li>
                    <li>Set the Following Permissions on Folder: "accounting", Group: "ACCOUNTANTS", Permissions: "Read/Write".</li>
                    <li>On Client-1, as feco.fona, Try to Access the Accountants Folder. It Should Fail.</li>
                    <li>Log Out of Client-1 as feco.fona.</li>
                    <li>On DC-1, Make feco.fona a Member of the "ACCOUNTANTS" Security Group.</li>
                    <li>Sign Back Into Client-1 as feco.fona, and Try to Access the "Accounting" Share in \\DC-1\.</li>
                </ul>
            <h3>- Step 1: Go Back to DC-1, in Active Directory, Create a Security Group Called "ACCOUNTANTS".</h3>
                <img src="https://i.imgur.com/0DZAei3.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/NfibiJd.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/GVXW5JM.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/f3ZglMW.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/QrwCMz9.png" height="50%" width="50%"/>
            <h3>- Step 2: Set the Following Permissions on Folder: "accounting", Group: "ACCOUNTANTS", Permissions: "Read/Write".</h3>
                <img src="https://i.imgur.com/Y7fAszf.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/KVt5ZjX.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/BMhnLvN.png" height="50%" width="50%"/>
            <h3>- Step 3: On Client-1, as feco.fona, Try to Access the Accountants Folder. It Should Fail.</h3>
                <img src="https://i.imgur.com/WIUu0zj.png" height="50%" width="50%"/>
            <h3>- Step 4: Log Out of Client-1 as "feco.fona".</h3>
                <img src="https://i.imgur.com/KBvg6Yp.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/ki3esRP.png" height="50%" width="50%"/>
            <h3>- Step 5: On DC-1, Make "feco.fona" a Member of the "ACCOUNTANTS" Security Group.</h3>
                <img src="https://i.imgur.com/mTgaiY3.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/OjdjEzY.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/ATrPMvL.png" height="50%" width="50%"/>
            <h3>- Step 6: Sign Back Into Client-1 as "feco.fona" And Try to Access the "Accounting" Share in \\DC-1\. It Now Should Work.</h3>
                <img src="https://i.imgur.com/Daj0bKS.png" height="20%" width="20%"/>
            <br>
                <img src="https://i.imgur.com/GGiag05.png" height="50%" width="50%"/>
                <img src="https://i.imgur.com/GlonHCL.png" height="50%" width="50%"/>
