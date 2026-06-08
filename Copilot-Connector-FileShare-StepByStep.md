# Step-by-Step: Integrate the Microsoft 365 Copilot File Share Connector

This guide walks you through configuring the **File Share Microsoft 365 Copilot connector** so users in your organization can search on-premises Windows file shares from Microsoft Search and Microsoft 365 Copilot.

> Source: [File Share connector – Microsoft Learn](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector)

---

## 1. Understand what the connector does

The File Share connector indexes content from on-prem Windows file shares so end users can:

- Ask natural-language questions about on-prem files in Copilot (e.g., summarize a document).
- Use Semantic Search for more accurate responses.
- Search the **textual content** of supported file formats: DOC, DOCM, DOCX, DOT, DOTX, EML, GIF, HTML, JPEG, JPG, MHT, MHTML, MSG, NWS, OBD, OBT, ODP, ODS, ODT, ONE, PDF, PNG, POT, PPS, PPT, PPTM, PPTX, TXT, XLB, XLC, XLSB, XLS, XLSX, XLT, XLXM, XML, XPS, ZIP.
- For multimedia and other types, **only metadata** is indexed.

### Known limits

| Limit | Value |
|---|---|
| Max file size indexed | 100 MB (larger files are skipped) |
| Max post-processed size per file | 4 MB (content beyond this isn't indexed) |
| Max file shares per connection | 20 (one per line in the file shares box) |

📚 **References**
- [File Share connector – Capabilities](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#capabilities)
- [File Share connector – Limitations](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#limitations)
- [Overview of Microsoft 365 Copilot connectors](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/overview)

---

## 2. Prerequisites

### 2.1 Roles & licensing
- A Microsoft 365 tenant with Microsoft 365 Copilot / Microsoft Search.
- **Search Administrator** (or equivalent) role in the Microsoft 365 admin center.
- A Windows user account with **read access** to all files in the target file shares.

### 2.2 Install the Microsoft Graph connector agent
1. Provision a Windows host on the **same network** as the file shares (for best latency/throughput).
2. Download and install the **Microsoft Graph connector agent** on that host.
3. Register the agent against your tenant.
   - Reference: [Install the Microsoft Graph connector agent](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/connector-agent#install-the-agent).

### 2.3 (If indexing legacy formats) Install VC++ Redistributable
If your shares contain old formats like `.msg` or `.doc`, install on the agent host:

- **Microsoft Visual C++ 2012 Redistributable Update 4 (x86 and x64)** — [Download](https://www.microsoft.com/download/details.aspx?id=30679)

### 2.4 Network & path prep
- Confirm the agent host can reach each file share via UNC path (`\\server\share`).
- If you plan to honor **Share ACLs**, ensure the path is in **UNC format** (set via *Sharing → Advanced Sharing*).
- Make sure user accounts/groups used for permissions are managed by **Active Directory**.

📚 **References**
- [File Share connector – Prerequisites](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#prerequisites)
- [Install the Microsoft Graph connector agent](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/connector-agent#install-the-agent)
- [Microsoft Graph connector agent overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/connector-agent)
- [Download Visual C++ Redistributable for Visual Studio 2012 Update 4](https://www.microsoft.com/download/details.aspx?id=30679)
- [About admin roles in the Microsoft 365 admin center](https://learn.microsoft.com/en-us/microsoft-365/admin/add-users/about-admin-roles)

---

## 3. Create the connection in the Microsoft 365 admin center

1. Sign in to the [Microsoft 365 admin center](https://admin.microsoft.com).
2. Navigate to **Settings → Search & intelligence → Data sources** (or **Connectors**).
3. Select **Add → File Share**.

📚 **References**
- [Set up Microsoft 365 Copilot connectors – deployment overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview)
- [Manage Microsoft Search](https://learn.microsoft.com/en-us/microsoftsearch/manage-search)

---

## 4. Configure connection settings

### 4.1 Name & description
- Provide a **Connection name** and **Connection ID**.
- Add a short description.

### 4.2 Choose a display name
- The display name appears next to indexed items in Copilot/Search and acts as a **content source filter**.
- Accept the default or customize to a name users recognize (e.g., "Finance File Share").

### 4.3 File Share on-premises URL
1. Enter the **file share path(s)** (UNC recommended). One per line, up to **20**.
2. Select the **previously installed Microsoft Graph connector agent**.
3. Enter the **Windows user credentials** with read access to all the files.

📚 **References**
- [File Share connector – Get Started (Display name, On-prem URL)](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#get-started)
- [Content source filters in Microsoft Search](https://learn.microsoft.com/en-us/microsoftsearch/custom-filters#content-source-filters)

---

## 5. Define limits for file indexing

Use these settings to exclude content that shouldn't be indexed.

### 5.1 By file type
- Choose to limit indexing to specific extensions or exclude certain ones.
- Remember: for non-text/multimedia formats, only **metadata** is indexed.

### 5.2 By last modified date
- Index only files **modified within N days**, or
- Index only files **modified since a specific date**.

### 5.3 By full network path or regex
- In path patterns, **escape special characters with `\`**.
  - Example: To target `\\CONTOSO\FILE\SHAREDFOLDER`, enter:
    ```
    \\\\CONTOSO\\FILE\\SHAREDFOLDER
    ```
- Regex reference: [Regular Expression Language Quick Reference](https://learn.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference).

### 5.4 Exceptions to limit rules
- Add **exception rules** (folder or file paths) to force-include items.
- **Exception rules take priority** over limit rules.

![Subset of files excluded from indexing with exception rules](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/media/file-connector/exclusionrule.png)

*Image source: [File Share connector – Full network path or regular expression](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#full-network-path-or-regular-expression) (© Microsoft)*

📚 **References**
- [File Share connector – Limits for file indexing](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#limits-for-file-indexing)
- [File Share connector – Full network path or regex](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#full-network-path-or-regular-expression)
- [.NET – Regular Expression Language Quick Reference](https://learn.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference)

---

## 6. (Optional) Preserve last access time

When the connector crawls a file, the file's **Last Access Time** metadata is updated by default. If archiving or backup tools depend on this timestamp:

- Enable **Preserve last access time** so the connector doesn't modify it during crawls.

📚 **References**
- [File Share connector – Preserve last access time](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#preserve-last-access-time)
- [Windows – fsutil behavior (disable last access)](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-behavior)

---

## 7. (Optional) Custom setup — enrich indexed data

Create custom properties derived from default ones to enrich search results.

![Custom property setup with a rule for URL](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/media/file-connector/connectors-custom-property-setup.png)

*Image source: [File Share connector – Custom setup](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#custom-setup) (© Microsoft)*

To add a custom property:

1. Enter a **property name** (this appears in search results).
2. Choose the value type: **Static** or **String/Regex Mapping**.
3. Click **Edit value**.
4. If **Static** → enter the literal string.
5. If **String/Regex**:
   - **Property**: pick a default property.
   - **Sample value**: an example to preview against.
   - **Expression**: a regex (up to **3** expressions) extracting the desired portion.
   - **Create formula**: combine extracted values into the final output.

📚 **References**
- [File Share connector – Custom setup](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#custom-setup)
- [.NET – Regular Expression Language Quick Reference](https://learn.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference)

---

## 8. Property labels & schema

Apply the **general setup instructions** for:

- **Property labels** — tag connector properties (Title, URL, Created, etc.) so they render properly in Search/Copilot.
- **Manage schema** — mark properties as Queryable, Searchable, Retrievable, Refinable.

📚 **References**
- [File Share connector – Property labels](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#property-labels)
- [File Share connector – Manage schema](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-schema)
- [Deployment overview – Assign property labels](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview)
- [Microsoft Graph – externalConnectors schema resource type](https://learn.microsoft.com/en-us/graph/api/resources/externalconnectors-schema)

---

## 9. Manage search permissions

On the **Access Permissions** screen you decide *who can see this data source's content in the search/Copilot results*. There are three options:

| Option | Sub-option | When to choose |
|---|---|---|
| **Only people with access to this data source** *(Recommended)* | **NTFS file permissions** | You want the most granular, file/folder-level security based on NTFS ACLs |
| | **File share permissions** | You manage access at the share level only (UNC shares) |
| **Everyone** | – | You don't use AD for identity, or you intentionally want unrestricted search results |

> ⚠️ **Critical concept:** The connector mirrors whatever ACLs exist on Windows. If those ACLs are misconfigured, the trimming in Copilot/Search will be wrong too. The work below — done on the **Windows file server** — is what actually controls who sees what.
>
> Identities used in ACLs **must be managed by Active Directory** (on-prem AD, synced to Microsoft Entra ID via Microsoft Entra Connect). Local Windows accounts and unsynced groups are not honored.

📚 **References (apply to all of Section 9)**
- [File Share connector – Manage search permissions](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-search-permissions)
- [Microsoft Graph connectors – Identities and access control](https://learn.microsoft.com/en-us/graph/connecting-external-content-manage-items#identity-and-permissions)
- [What is Microsoft Entra Connect Sync?](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/whatis-azure-ad-connect)

---

### 9.1 Prerequisites for ACL-based trimming (applies to both NTFS and Share options)

Before publishing the connection, verify on the file server:

1. The server is **domain-joined** to your on-prem AD.
2. Users and groups in your ACLs are **AD security principals** (not local users / not distribution groups).
3. On-prem AD is **synchronized to Microsoft Entra ID** via Microsoft Entra Connect, so each user's `objectSID` maps to their cloud identity.
4. The **service account** used by the connector has **read access** to every share you plan to crawl (so it can enumerate files and ACLs).
5. The connector agent host can resolve the file server by its **FQDN** and reach the AD domain controller.

📚 **References**
- [Active Directory security groups](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups)
- [Microsoft Entra Connect Sync – How it works](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-sync-whatis)
- [Join a computer to a domain](https://learn.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)

---

### 9.2 Option A — "Only people with access" → **NTFS file permissions** *(Recommended)*

Use this when you want true file- and folder-level trimming. The connector reads the **NTFS ACL** on every indexed file and only returns results to users granted Read.

**Windows-side configuration (do this on the file server, per folder/file):**

1. Open **File Explorer** on the file server.
2. Right-click the root folder of the share → **Properties** → **Security** tab.
3. Click **Edit…** (or **Advanced** for more control).
4. **Remove** any broad principals you don't want indexed against, e.g.:
   - `Everyone`
   - `Authenticated Users` (if too broad)
   - `Users` (local)
5. **Add** the AD groups/users that should see the content and grant them at least **Read & execute**, **List folder contents**, **Read**.
6. Click **Advanced** → confirm **inheritance** is enabled if you want child folders to inherit, or break inheritance and set explicit ACLs on sensitive subfolders.
7. Apply and verify with: right-click folder → **Properties** → **Security** → **Advanced** → **Effective Access** tab → check a sample user.

**PowerShell verification (run on the file server):**

```powershell
# View NTFS ACL on a folder
Get-Acl "D:\Shares\Finance" | Format-List

# View effective permissions for a specific user (requires the NTFSSecurity module)
Install-Module NTFSSecurity -Scope CurrentUser
Get-NTFSEffectiveAccess -Path "D:\Shares\Finance" -Account "CONTOSO\jdoe"
```

**Common pitfalls:**
- Leaving `Everyone: Read` or `Authenticated Users: Read` on NTFS → connector treats it as world-readable.
- Granting access via a **local** group (e.g., `BUILTIN\Users`) instead of an AD group → won't trim in cloud.
- Using **Deny** ACEs heavily — Deny wins over Allow; double-check effective access.

📚 **References**
- [Access Control Lists (ACLs) – Windows Server](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists)
- [File and folder permissions (NTFS)](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc787794(v=ws.10))
- [Get-Acl (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-acl)
- [Set-Acl (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-acl)
- [NTFSSecurity PowerShell module – PowerShell Gallery](https://www.powershellgallery.com/packages/NTFSSecurity)
- [How permissions are calculated (Allow/Deny precedence)](https://learn.microsoft.com/en-us/windows/win32/secauthz/order-of-aces-in-a-dacl)

---

### 9.3 Option B — "Only people with access" → **File share permissions**

Use this when access is managed at the **SMB share** level (the "Share Permissions" tab) rather than NTFS. Requires the path you entered in the connector to be in **UNC format** (`\\server\share`).

![Advanced Sharing dialog for a Windows file share](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/media/file-connector/file-advanced-sharing.png)

*Image source: [File Share connector – Manage search permissions](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-search-permissions) (© Microsoft)*

**Windows-side configuration (per share):**

1. On the file server, open **File Explorer**.
2. Right-click the folder you've shared → **Properties** → **Sharing** tab → **Advanced Sharing…**.
3. Check **Share this folder**; confirm the **Share name** (this is what appears after `\\server\`).
4. Click **Permissions**.
5. **Remove `Everyone`** (Windows adds this by default with Read access — leaving it in means every searcher sees every file).
6. **Add** AD groups/users who should be able to search this content and grant **Read** (or **Change** / **Full Control** as appropriate for the source workflow — the connector only needs Read for trimming).
7. Click **OK** → **OK**.
8. Confirm the share appears as `\\<server-FQDN>\<share-name>` and that this exact UNC path is what you entered in the connector's **File Share on-premises URL**.

**PowerShell verification:**

```powershell
# List share permissions
Get-SmbShareAccess -Name "Finance"

# Remove Everyone, grant an AD group Read
Revoke-SmbShareAccess -Name "Finance" -AccountName "Everyone" -Force
Grant-SmbShareAccess  -Name "Finance" -AccountName "CONTOSO\Finance-Readers" -AccessRight Read -Force
```

**Important interactions:**
- The **effective access** for a user opening a file = the **more restrictive** of Share ACL ∩ NTFS ACL. The connector, however, only trims on the option you selected (Share *or* NTFS). Keep the two in sync to avoid "I can search but can't open" surprises.
- If the path is a local path (`D:\Shares\Finance`) rather than UNC, the **File share permissions** option is unavailable — re-enter the share as `\\server\share`.

📚 **References**
- [File Share connector – UNC path / Share ACL note](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-search-permissions)
- [SMB share – overview](https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview)
- [Get-SmbShareAccess (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/smbshare/get-smbshareaccess)
- [Grant-SmbShareAccess (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/smbshare/grant-smbshareaccess)
- [Revoke-SmbShareAccess (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/smbshare/revoke-smbshareaccess)
- [UNC path format](https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats#unc-paths)

---

### 9.4 Option C — **Everyone**

Use this only when:
- You don't manage identities in AD, or
- The data is genuinely public to the entire organization, or
- You're doing a proof of concept and will lock down later.

**What happens:**
- Every licensed user in your tenant sees every indexed item in search/Copilot results.
- When they **click through to open** the file, Windows still enforces the source NTFS/Share ACLs — so they may see the result but be denied on open. This leaks file existence, names, and snippets.
- **Do not use** for sensitive data.

No Windows-side ACL changes are required, but you may still want to remove `Everyone: Read` from sensitive folders so the open-file step is denied at the source.

📚 **References**
- [File Share connector – 'Everyone' option behavior](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-search-permissions)
- [Microsoft 365 Copilot – Data, privacy, and security](https://learn.microsoft.com/en-us/copilot/microsoft-365/microsoft-365-copilot-privacy)

---

### 9.5 Decision quick-reference

```
Is the data sensitive?
├── Yes → Use "Only people with access"
│         ├── Need file/folder granularity? → NTFS file permissions  ✅ recommended
│         └── Access already managed at the share only? → File share permissions (UNC required)
└── No  → "Everyone" is acceptable for org-wide content
```

### 9.6 Post-publish validation of permission trimming

1. Open the **Index browser** in the M365 admin center and inspect a known-restricted file's ACL.
2. Sign into Microsoft Search / Copilot as a user **with** access → confirm the file appears.
3. Sign in as a user **without** access → confirm the file does **not** appear in results.
4. If trimming looks wrong:
   - Re-check the AD group membership of the test user (and force a Microsoft Entra Connect sync).
   - Re-check the ACL choice in the connector matches what you actually configured (Share vs NTFS).
   - Re-check that no broad principal (`Everyone`, `Authenticated Users`) is still on the ACL.
   - Trigger a **full crawl** so updated ACLs are picked up.

📚 **References**
- [Index browser – Validate indexed content & permissions](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/indexed-content)
- [Force a Microsoft Entra Connect sync cycle (Start-ADSyncSyncCycle)](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-sync-feature-scheduler)
- [Troubleshoot the File Share connector](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/troubleshoot-file-share-connector)

---

## 10. Configure synchronization (crawl) schedule

Set how often the connector syncs with your file shares:

- **Full crawl** — re-indexes everything (use sparingly).
- **Incremental crawl** — picks up changes since last crawl (use frequently).

Adjust defaults to balance freshness vs. load.

📚 **References**
- [File Share connector – Synchronization](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#synchronization)
- [Deployment overview – Guidelines for crawl settings](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview#guidelines-for-crawl-settings)

---

## 11. Review, publish, and test

1. **Review** all settings on the summary page.
2. **Publish** the connection — the initial crawl will begin.
3. Monitor progress in **Admin center → Connectors**.
4. Validate indexed content and permissions with the **[Index browser](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/indexed-content)**.
5. (Optional, Microsoft Search) **Customize the search results page** — see [Step 11 in deployment overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview#step-11-customize-the-search-results-page).
6. Run end-user validation:
   - In **Microsoft Search**, query a known file name/term.
   - In **Microsoft 365 Copilot**, ask a natural-language question about a known document (e.g., "Summarize the Q3 budget memo from the Finance share").

📚 **References**
- [File Share connector – Review and test your connection](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#review-and-test-your-connection)
- [Index browser – indexed content](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/indexed-content)
- [Customize the search results page](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview#step-11-customize-the-search-results-page)

---

## 12. Operate & troubleshoot

- Monitor the connection state in the **Connectors** section of the [admin center](https://admin.microsoft.com).
- Manage updates/deletions: [Manage your connector](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/manage-connector).
- Common issues: [Troubleshoot the File Share connector](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/troubleshoot-file-share-connector).
- FAQ: [Copilot connectors – Frequently Asked Questions](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/frequently-asked-questions).
- Support: [Microsoft Graph | Support](https://developer.microsoft.com/en-us/graph/support).

📚 **References**
- [File Share connector – Troubleshooting](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#troubleshooting)
- [Manage your connector](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/manage-connector)
- [Troubleshoot the File Share connector](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/troubleshoot-file-share-connector)
- [Microsoft Graph connectors – FAQ](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/frequently-asked-questions)

---

## 13. Indexing latency — "How long until a new file is searchable in Copilot?"

There is **no fixed SLA** for how quickly a newly added file becomes discoverable in Microsoft Search and Microsoft 365 Copilot. End-to-end latency is the sum of three configurable / variable stages:

| Stage | Typical value | What controls it |
|---|---|---|
| 1. Wait for next **incremental crawl** | as low as **15 minutes**, up to **12 hours** (configurable). Recurrence can also be daily / weekly / biweekly / every 4 weeks. | Connection → **Sync** tab → **Refresh settings** |
| 2. **Crawl start drift** | up to **~1 hour** extra under system load | Microsoft platform behavior — *"System load can delay crawl start times by up to an hour."* |
| 3. **Indexing & ranking** in Search/Copilot | "varies based on the volume of data and the complexity of the data source" — no published number | Item size, total volume, current backlog, semantic ranker processing |

**Best case** (incremental set to 15 min, no load, single small file): roughly **15 minutes – ~1.5 hours**.
**Default / worst case** (default schedule, large backlog): many hours.

### 13.1 Important caveat — permissions are *only* updated on a full crawl

If you only **add a file** to an existing folder whose ACLs are unchanged, an **incremental crawl** is enough.

But if you also **changed permissions** (e.g., granted a new AD group access, removed `Everyone: Read`, broke inheritance), the incremental crawl will pick up the file but **not** the new ACL. The file may appear to the wrong users — or be hidden from the right ones — until a **full crawl** runs.

> "Incremental crawls don't currently process updates to permissions. Run full crawls periodically to maintain sync accuracy."
> — Microsoft Learn, *Deployment overview*

### 13.2 Recommended settings for "drop a file → quickly queryable in Copilot"

1. On the connection's **Sync** tab, set the **Incremental** crawl **Frequency** to **15 minutes** (the minimum allowed).
2. Keep the **Full** crawl on at least a **Daily** recurrence so deletions and ACL changes are captured within 24 hours.
3. After any bulk ACL / Share-permission change on the Windows file server, **trigger a manual full crawl** instead of waiting.
4. To confirm a specific file is in the index (and rule out "Copilot just hasn't picked it up yet"), open the **Index browser** in the M365 admin center and search for it by name or path. "Indexed" and "ranked into Copilot's answer" are two different stages — the Index browser confirms the first.

### 13.3 Quick troubleshooting decision tree

```
File not appearing for a user in Copilot?
├── Is it in the Index browser?
│   ├── No  → Crawl hasn't run yet OR file is excluded by size/type/path rules
│   │        → Wait for next incremental crawl, or trigger a manual crawl
│   └── Yes → It's indexed. Then check…
│             ├── Did you recently change ACLs? → Run a full crawl
│             ├── Is the test user in the granted AD group? → Force Entra Connect sync
│             ├── Is "include in All vertical" enabled? → Enable it
│             └── Is the file > 100 MB or unsupported type? → It won't be indexed at all
└── Big bulk import? → Indexing can take hours; let it complete before troubleshooting
```

### 13.4 Sources (public Microsoft Learn articles confirming the above)

- **File Share connector – Synchronization** (full vs incremental, refresh interval is configurable):
  <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#synchronization>
- **Deployment overview – Crawl scheduling** (15 min – 12 hr frequency; default schedule selected by connector type; ~1 hr load drift; overlap behavior):
  <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview#crawl-scheduling>
- **Deployment overview – Guidelines for crawl settings** (when to use full vs incremental; permissions note):
  <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview#guidelines-for-crawl-settings>
- **Copilot connectors FAQ – Full vs incremental crawl** (incremental does **not** process permission updates):
  <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/frequently-asked-questions#what-is-the-difference-between-full-crawl-and-an-incremental-crawl->
- **Copilot connectors FAQ – Why aren't results displayed in Microsoft Search?** (explicit "indexing delays vary with data volume and complexity"; All-vertical setting; file-type/size limits):
  <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/frequently-asked-questions#why-aren-t-results-displayed-in-microsoft-search->
- **Indexed content (Index browser)** — how to verify a file is in the index:
  <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/indexed-content>

---

## Quick checklist

- [ ] Graph connector agent installed, registered, on same network as shares
- [ ] (If needed) VC++ 2012 Redistributable Update 4 installed on agent host
- [ ] Service account has Read access to all target shares
- [ ] Share paths in UNC format (if using Share ACLs)
- [ ] Connection created in admin center with display name set
- [ ] Up to 20 shares listed, special chars escaped properly
- [ ] Limit rules + exception rules configured
- [ ] Permissions model chosen (NTFS / Share / Everyone) and matching Windows ACLs cleaned up (no stray `Everyone` / `Authenticated Users`)
- [ ] AD groups used in ACLs are synced to Microsoft Entra ID
- [ ] Effective access verified on the file server for a sample user
- [ ] Refresh intervals set (full + incremental)
- [ ] Connection published, initial crawl completes
- [ ] Validated via Index browser, Microsoft Search, and Copilot



## IP Firewall Rules

If your data source uses IP firewall rules, allow access to the Copilot connectors service by permitting the following IP ranges.

| Region | Microsoft 365 Enterprise | Microsoft 365 Government |
|--------|--------------------------|--------------------------|
| NAM    | 52.250.92.252/30, 52.224.250.216/30 | 52.245.230.216/30, 20.141.117.64/30 |
| EUR    | 20.54.41.208/30, 51.105.159.88/30   | N/A |
| APC    | 52.139.188.212/30, 20.43.146.44/30  | N/A |

---

## Appendix A — Consolidated reference list

### Primary Microsoft Learn articles (File Share connector)
| Topic | URL |
|---|---|
| File Share connector (main) | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector> |
| Capabilities | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#capabilities> |
| Limitations | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#limitations> |
| Prerequisites | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#prerequisites> |
| Get Started | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#get-started> |
| Limits for file indexing | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#limits-for-file-indexing> |
| Full network path / regex | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#full-network-path-or-regular-expression> |
| Preserve last access time | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#preserve-last-access-time> |
| Custom setup | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#custom-setup> |
| Property labels | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#property-labels> |
| Manage schema | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-schema> |
| Manage search permissions | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#manage-search-permissions> |
| Synchronization | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#synchronization> |
| Review and test | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#review-and-test-your-connection> |
| Troubleshooting | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/fileshare-connector#troubleshooting> |

### Supporting Copilot connectors docs
| Topic | URL |
|---|---|
| Deployment overview | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/deployment-overview> |
| Microsoft Graph connector agent | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/connector-agent> |
| Install the connector agent | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/connector-agent#install-the-agent> |
| Connector agent release notes | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/connector-agent-releases> |
| Indexed content / Index browser | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/indexed-content> |
| Manage your connector | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/manage-connector> |
| Troubleshoot File Share connector | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/troubleshoot-file-share-connector> |
| Connectors FAQ | <https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/frequently-asked-questions> |
| Microsoft Search – Content source filters | <https://learn.microsoft.com/en-us/microsoftsearch/custom-filters#content-source-filters> |

### Windows / Active Directory references (Section 9)
| Topic | URL |
|---|---|
| Access Control Lists (ACLs) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists> |
| Order of ACEs in a DACL (Allow vs Deny) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/order-of-aces-in-a-dacl> |
| Active Directory security groups | <https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups> |
| SMB share overview | <https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview> |
| Get-Acl | <https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-acl> |
| Set-Acl | <https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-acl> |
| Get-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/get-smbshareaccess> |
| Grant-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/grant-smbshareaccess> |
| Revoke-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/revoke-smbshareaccess> |
| NTFSSecurity PS module | <https://www.powershellgallery.com/packages/NTFSSecurity> |
| UNC path format | <https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats#unc-paths> |
| fsutil behavior (last access time) | <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-behavior> |

### Identity / Entra references
| Topic | URL |
|---|---|
|| Access Control Lists (ACLs) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists> |
| Order of ACEs in a DACL (Allow vs Deny) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/order-of-aces-in-a-dacl> |
| Active Directory security groups | <https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups> |
| SMB share overview | <https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview> |
| Get-Acl | <https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-acl> |
| Set-Acl | <https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-acl> |
| Get-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/get-smbshareaccess> |
| Grant-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/grant-smbshareaccess> |
| Revoke-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/revoke-smbshareaccess> |
| NTFSSecurity PS module | <https://www.powershellgallery.com/packages/NTFSSecurity> |
| UNC path format | <https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats#unc-paths> |
| fsutil behavior (last access time) | <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-behavior> |

### Other
| Topic | URL |
|---|---|
|| Access Control Lists (ACLs) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists> |
| Order of ACEs in a DACL (Allow vs Deny) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/order-of-aces-in-a-dacl> |
| Active Directory security groups | <https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups> |
| SMB share overview | <https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview> |
| Get-Acl | <https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-acl> |
| Set-Acl | <https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-acl> |
| Get-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/get-smbshareaccess> |
| Grant-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/grant-smbshareaccess> |
| Revoke-SmbShareAccess | <https://learn.microsoft.com/en-us/powershell/module/smbshare/revoke-smbshareaccess> |
| NTFSSecurity PS module | <https://www.powershellgallery.com/packages/NTFSSecurity> |
| UNC path format | <https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats#unc-paths> |
| fsutil behavior (last access time) | <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-behavior> |

---

## Appendix B — Acronyms

> Functional acronyms used in this guide. File-format extensions (DOC, DOCX, PDF, PPT, XLS, ZIP, etc.) are omitted because they are standard document/archive formats supported by the connector for content indexing.

| Access Control Lists (ACLs) | <https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists> 
| SMB share overview | <https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview> |
| NTFS Overview | <https://learn.microsoft.com/en-us/windows-server/storage/file-server/ntfs-overview> |
| UNC path format | <https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats#unc-paths> |

