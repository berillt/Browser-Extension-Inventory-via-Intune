<#
    .USAGE / KULLANIM
    TR: Bu script, Microsoft Intune (Endpoint Manager) üzerinden "Scripts" bölümünden dağıtılmalıdır.
    EN: This script should be deployed via Microsoft Intune (Endpoint Manager) under the "Scripts" section.

    TR: Çalıştırma Ayarları:
    - Bu scripti oturum açmış kullanıcı kimlik bilgilerini kullanarak çalıştır: HAYIR (SYSTEM yetkisi gerekir)
    - Script imza kontrolünü zorunlu tut: HAYIR
    - Scripti 64 bit PowerShell Host'ta çalıştır: EVET
    
    EN: Execution Settings:
    - Run this script using the logged on credentials: NO (Requires SYSTEM privileges)
    - Enforce script signature check: NO
    - Run script in 64 bit PowerShell Host: YES

    .DESCRIPTION
    TR: Cihazdaki tüm kullanıcı profillerini tarar ve Chrome/Edge eklenti ID'lerini merkezi bir ağ paylaşımına aktarır.
    EN: Scans all user profiles on the device and exports Chrome/Edge extension IDs to a central network share.
    
    .NOTES
    TR: Intune üzerinden "SYSTEM" hesabı ile çalıştırılmalıdır.
    EN: Must be executed via Intune using the "SYSTEM" account.
#>

# ---------------------------------------------------------
# 1. SETTINGS / AYARLAR
# ---------------------------------------------------------

# TR: Verilerin toplanacağı merkezi ağ yolu
# EN: Central network path where data will be collected
$LogPath = "\\YOUR_SERVER_NAME\YOUR_SHARE_PATH$"

# TR: Tarayıcıların eklenti klasör yolları
# EN: Extension folder paths for browsers
$BrowserPaths = @(
    "AppData\Local\Google\Chrome\User Data\Default\Extensions",
    "AppData\Local\Microsoft\Edge\User Data\Default\Extensions"
)

$Results = @()

# ---------------------------------------------------------
# 2. DATA COLLECTION / VERİ TOPLAMA
# ---------------------------------------------------------

# TR: C:\Users altındaki gerçek kullanıcıları al (Sistem profillerini hariç tutar)
# EN: Get real user profiles under C:\Users (Excludes system profiles)
$UserProfiles = Get-ChildItem "C:\Users" | Where-Object { 
    $_.PSIsContainer -and $_.Name -notmatch "Public|Default|All Users|desktop.ini" 
}

foreach ($Profile in $UserProfiles) {
    foreach ($BPath in $BrowserPaths) {
        # TR: Profil bazlı tam yolu oluştur
        # EN: Construct the full profile-based path
        $FullExtensionPath = Join-Path "C:\Users\$($Profile.Name)" $BPath
        
        if (Test-Path $FullExtensionPath) {
            # TR: Klasör isimlerini (Eklenti ID'leri) al
            # EN: Get folder names (Extension IDs)
            $Extensions = Get-ChildItem $FullExtensionPath -ErrorAction SilentlyContinue | Where-Object { $_.PSIsContainer }
            
            foreach ($Ext in $Extensions) {
                $Results += [PSCustomObject]@{
                    Timestamp    = Get-Date -Format "yyyy-MM-dd HH:mm"
                    ComputerName = $env:COMPUTERNAME
                    UserName     = $Profile.Name
                    Browser      = if ($BPath -like "*Chrome*") { "Chrome" } else { "Edge" }
                    ExtensionID  = $Ext.Name
                }
            }
        }
    }
}

# ---------------------------------------------------------
# 3. EXPORT / DIŞA AKTARIM
# ---------------------------------------------------------

# TR: Eğer eklenti bulunduysa CSV olarak kaydet
# EN: If extensions are found, export as CSV
if ($Results.Count -gt 0) {
    if (Test-Path $LogPath) {
        try {
            # TR: Her bilgisayar için benzersiz dosya adı oluştur
            # EN: Create a unique filename for each computer
            $FileName = "$($env:COMPUTERNAME)_Extensions.csv"
            $FinalPath = Join-Path $LogPath $FileName
            
            $Results | Export-Csv -Path $FinalPath -NoTypeInformation -Encoding UTF8 -Force
        }
        catch {
            Write-Error "Error: Could not write to network path. / Hata: Ag yoluna yazilamadi: $($_.Exception.Message)"
        }
    }
    else {
        Write-Error "Error: Log path not found. / Hata: Log yolu bulunamadi: $LogPath"
    }
}
