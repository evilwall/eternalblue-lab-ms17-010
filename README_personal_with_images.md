# 🔓 EternalBlue Exploitation Lab – MS17-010

## 📘 Sammanfattning
Detta projekt är en dokumentation av en labb jag själv byggde och genomförde i en isolerad VirtualBox-miljö. Målet var att praktiskt visa hur en osäker Windows Server 2012 R2 kan utnyttjas med hjälp av sårbarheten **MS17-010 (EternalBlue)**. Under övningen genomförde jag rekognosering, exploatering och post-exploitation, samt analyserade nätverkstrafik för att se hur känslig data kan läcka.

![Nmap-skanning som visar SMB 445 öppen](images/nmap_scan.png)

---

## 🎯 Mitt syfte
Jag ville träna på hela arbetsflödet i ett penetrationstest: från att identifiera en sårbar tjänst, till att utnyttja den, och slutligen visa vilka risker en angripare kan orsaka. Fokus låg på att få en Meterpreter-session via EternalBlue och sedan testa olika post-exploitation-tekniker.

---

## 🧪 Labbinformation

| Roll             | OS                        | IP             | Verktyg/Tjänster                      |
|------------------|---------------------------|----------------|---------------------------------------|
| Angripare        | Kali Linux 2023.2         | 192.168.1.20   | Metasploit, Wireshark                 |
| Mål (sårbar)     | Windows Server 2012 R2    | 192.168.1.2    | SMB 445 (sårbar), LDAP 389, Kerberos 88 |

> Båda maskinerna låg i ett host-only-nät (192.168.1.0/24) utan internetåtkomst.  
> Endast **SMB 445** var sårbar för EternalBlue. LDAP och Kerberos identifierades men påverkades inte.

---

## 🔍 Använda verktyg och moduler
- **Nmap** – för portskanning
- **Metasploit Framework**:
  - `auxiliary/scanner/smb/smb_ms17_010` – verifierade sårbarheten
  - `exploit/windows/smb/ms17_010_eternalblue` – körde själva attacken
- **Payload** – `windows/x64/meterpreter/reverse_tcp`
- **Wireshark** – användes för att snappa upp HTTP POST-trafik

---

## 🛠️ Så här gjorde jag

1. **Skanning med Nmap**  
   Jag skannade målmaskinen och såg att SMB-porten 445 var öppen.  

   ![Metasploit scanner visar host vulnerable](images/ms17_scanner.png)

2. **Verifiera sårbarheten**  
   Med Metasploit körde jag scannern för MS17-010 och fick bekräftat att servern var sårbar.

3. **Exploatering**  
   Jag använde EternalBlue-modulen och satte payload till `windows/x64/meterpreter/reverse_tcp`. Resultatet blev att jag fick en Meterpreter-session med SYSTEM-behörighet.  

   ![Exploitering lyckades, Meterpreter session öppnad](images/meterpreter_session.png)

4. **Post-exploitation**  
   Jag använde Meterpreter för att logga tangenttryckningar och testa filöverföring.  

   ![Keyscan visar tangenttryckningar](images/keylogger.png)

5. **Nätverkssniffning**  
   Jag körde Wireshark och fångade ett okrypterat HTTP POST-anrop. Där kunde jag läsa ut användarnamn och lösenord i klartext.  

   ![Wireshark POST med användarnamn och lösenord](images/wireshark_post.png)

---

## ✅ Mina resultat
- Jag fick full SYSTEM-åtkomst till Windows Servern via SMB 445.  
- Jag kunde logga tangenttryckningar och manipulera filer.  
- Jag visade att okrypterad nätverkstrafik lätt kan avlyssnas.  

## 🧠 Mina lärdomar
- Att alltid patcha system (MS17-010 var en mycket allvarlig sårbarhet).  
- Att segmentera nätverk så att SMB inte är tillgängligt för obehöriga.  
- Att all autentisering bör ske över TLS.  
- Att logghantering centralt är viktigt för att förhindra att en angripare kan dölja spår.  

---

## 👤 Om mig
**Emil Walls**  
IT-säkerhetsstudent – TUC Yrkeshögskola  
LIA2-period: 12 jan – 1 maj 2026
