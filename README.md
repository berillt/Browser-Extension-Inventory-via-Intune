# Browser-Extension-Inventory-via-Intune
TR: Tarayıcı Eklenti Envanteri Toplama (Intune & PowerShell)
EN: Browser Extension Inventory Collection (Intune & PowerShell)

  TR: Proje Hakkında
Microsoft Defender for Endpoint veya standart envanter araçlarının eklenti görünürlüğü kısıtlı olduğunda, bu proje tüm istemcilerden Chrome ve Edge eklenti ID'lerini toplamak için maliyetsiz bir çözüm sunar.
  
  EN: About the Project
When browser extension visibility is limited in standard inventory tools, this project provides a cost-free solution to collect Chrome and Edge extension IDs from all clients.

    TR: 1. Aşama: Altyapı Hazırlığı (Ağ Paylaşımı)
Scriptin çalışması için verilerin toplanacağı merkezi bir klasör oluşturulmalıdır.

Gizli Paylaşım: Klasör isminin sonuna $ ekleyerek (Örn: Extensions$) klasörü ağda görünmez yapın.

Yetkilendirme (Kritik): * Sharing & NTFS Permissions: "Domain Computers" grubuna Yazma/Modify yetkisi verilmelidir. (Script SYSTEM hesabıyla çalıştığı için bu yetki şarttır).

Analiz Yetkisi: Kendi kullanıcı hesabınıza sadece Okuma yetkisi vermeniz yeterlidir.

    EN: Step 1: Infrastructure Setup (Network Share)
A central folder must be created to collect the data.

Hidden Share: Add $ to the folder name (e.g., Extensions$) to make it invisible on the network.

Authorization (Critical):

Sharing & NTFS Permissions: Assign Write/Modify permissions to the "Domain Computers" group. (This is required as the script runs under the SYSTEM account).

Analysis Permission: Read-only access for your own admin account is sufficient.

    TR: 2. Aşama: Intune Dağıtımı
Collector.ps1 dosyasını Microsoft Intune üzerinden şu ayarlarla dağıtın:

Run this script using the logged on credentials: NO (SYSTEM yetkisi için).

Enforce script signature check: NO.

Run script in 64 bit PowerShell Host: YES.

    EN: Step 2: Intune Deployment
Deploy the Collector.ps1 file via Microsoft Intune with these settings:

Run this script using the logged on credentials: NO (for SYSTEM privileges).

Enforce script signature check: NO.

Run script in 64 bit PowerShell Host: YES.

    TR: 3. Aşama: Veri Birleştirme ve Analiz
Toplanan binlerce CSV dosyasını anlamlı bir rapora dönüştürmek için şu adımlar izlenir:

Birleştirme (Sunucu Tarafında): Ağ klasöründeki tüm dosyalar tek bir ham listede toplanır.

Gruplandırma: Hangi eklentiden kaç adet olduğu hesaplanır.

Mağaza Eşleştirme (Admin Makinesinde): ID'ler üzerinden Google ve Edge mağazalarından eklenti isimleri "Web Scraping" yöntemiyle çekilir.

    EN: Step 3: Data Merging and Analysis
Follow these steps to transform collected CSVs into a meaningful report:

Merging (Server Side): Consolidate all files in the network share into a single raw list.

Grouping: Calculate the count of each unique extension.

Store Matching (Admin PC): Fetch extension names from Google and Edge stores using Web Scraping based on IDs.

    TR: Neden Bu Yöntem?
%100 Görünürlük: Defender'ın göremediği "pasif" eklentileri bile yakalar.

Ücretsiz: Ek lisans maliyeti gerektirmez.

Hızlı: Binlerce cihazın verisini dakikalar içinde raporlar.

    EN: Why This Method?
100% Visibility: Captures even "passive" extensions that Defender might miss.

Zero Cost: No additional licensing required.

Fast: Reports data from thousands of devices in minutes.
