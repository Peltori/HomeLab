# Proxmox-lab: ParrotOS etätyöpöytä ympäristön rakentaminen
  
## 1. Yhteenveto  
Rakensin Proxmox tyypin-1 hypervisoria hyödyntäen ParrotOS 7 virtuaalikoneen Hack The Box -harjoittelua varten, jotta saan siirrettyä kuormaa pois omalta työasemalta. Tavoitteena oli löytää riittävän sulava ratkaisu etätyöpöytä käyttöön Wayland-ympäristössä ilman tarpeettomia kolmannen osapuolen sovelluksia.  
  
## 2. Tavoite  
- Vapauttaa oman koneen resursseja
- Käyttää ParrotOS-ympäristöä Proxmoxissa etänä
- Löytää käytännöllinen remote desktop -ratkaisu HTB-käyttöön
- Dokumentoida valinnat, testit ja havainnot
  
## 3. Ympäristö  
### Host  
- Käyttöjärjestelmä: Fedora 43
- Window manager / Desktop: Hyprland
- Näyttöprotokolla / Display Server: Wayland  
  
### Hypervisor  
- Proxmox VE: 
- Verkko: 
- Storage: 
  
### Guest  
- Distro: ParrotOS 7
- Desktop environment: KDE Plasma
- Näyttöprotokolla: Wayland
- VM-resurssit:
	- vCPU: 4 
	- RAM:  8GB
	- Disk:  100GB
	- Display:
  
## 4. Lähtötilanne  
Alkuperäinen tavoite oli käyttää Proxmoxissa pyörivää ParrotOS-virtuaalikonetta päivittäiseen HTB-työskentelyyn. Proxmoxin oma web console osoittautui liian lagiseksi jatkuvaan työpöytäkäyttöön, joten tarvittiin parempi ratkaisu.  
  
## 5. Ongelma  
Mikä remote desktop -ratkaisu toimii riittävän hyvin Linux host -> Proxmox -> Linux guest -ketjussa, kun sekä host, että guest käyttävät Waylandia?  
  
## 6. Arvioidut vaihtoehdot  
  
### Remmina / RDP  
**Plussat**  
- Yleinen ja tunnettu  
- Helppo käyttää clienttinä  
  
**Miinukset**  
- Wayland + KDE + Linux guest ei välttämättä paras yhdistelmä  
- Ei vaikuttanut parhaalta omassa käyttötapauksessa  
  
**Johtopäätös**  
- Ei ensimmäinen valinta tähän tarkoitukseen
  
### NoMachine  
**Plussat**  
- Vaikuttaa tarjoavan paremman desktop-kokemuksen kuin perus console-ratkaisut  
- Linux client + Linux host mahdollinen
  
**Miinukset**
- Wayland/KDE-tuki ei vaikuttanut täysin ongelmattomalta
- Lisää yksi erillinen stack hallittavaksi
  
**Johtopäätös**  
- Mahdollinen fallback-vaihtoehto
  
### KDE Remote Desktop  
**Plussat**
- Natiivimpi KDE/Wayland-suunta
  
**Miinukset**  
- Vaikutti vielä epävarmalta käytännön daily use- ratkaisuksi
  
**Johtopäätös**  
- Ei ensimmäinen ratkaisu
  
### Sunshine / Moonlight  
**Plussat**  
- Potentiaalisesti sulava kokemus  
  
**Miinukset**  
- Liikaa säätöä tähän tarpeeseen  
- Ylimitoitettu HTB-labraan tässä vaiheessa  
  
**Johtopäätös**  
- Ei alkuvaiheen valinta
- Jälleen useampi liikkuva osa, vaikeuttamaan ylläpidettävyyttä
  
### SPICE  
**Plussat**  
- Saatavilla suoraan Proxmoxissa
- Yksinkertainen tapa parantaa kokemusta verrattuna web consoleen
- Riittävän hyvä työpöytäkäyttöön
  
**Miinukset**  
- Clipboard ei välttämättä toimi luotettavasti Wayland/KDE/Hyprland-yhdistelmissä
  
**Johtopäätös**  
- Paras ensimmäinen ratkaisu tähän käyttötarkoitukseen
  
## 7. Päätös  
Valitsin ensimmäiseksi toteutukseksi SPICE-ratkaisun, koska se on yksinkertainen, suoraan Proxmoxin tukema ja todennäköisesti riittävän hyvä HTB-tyyppiseen käyttöön ilman, että ympäristöstä tulee oma monimutkainen projekti.
  
## 8. Toteutus  
### Proxmox-puoli
- VM luotu Proxmoxiin
- SPICE otettu käyttöön
- Clientiksi suunniteltu virt-viewer / remote-viewer

### Guest-puoli  
- ParrotOS 7 asennettu  
- KDE Plasma käytössä  
- Wayland käytössä  
- Mahdollinen lisäpaketti:  
- `spice-vdagent`
- `qemu-quest`
  
## 9. Testit  
### Testattavat asiat  
- Desktopin yleinen tuntuma
- Ikkunoiden liikuteltavuus
- Syötteen viive
- Clipboard host -> guest
- Clipboard guest -> host
- Resoluutio ja skaalautuvuus
- Toimivuus HTB-työkaluilla
  
### Testitulokset  
| Testi | Tulos | Huomiot |  
|---|---|---|  
| Proxmox web console | | |  
| SPICE desktop responsiveness | | |  
| Clipboard host -> guest | | |  
| Clipboard guest -> host | | |  
| Yleinen käytettävyys | | |  
  
## 10. Havaintoja  
- Proxmoxin oma console sopii hallintaan, mutta ei mukavaan työpöytäkäyttöön  
- SPICE vaikutti järkevimmältä kompromissilta käytettävyyden ja vaivan välillä  
- Wayland-pohjaisissa ympäristöissä clipboard-integraatio ei ole aina luotettava  
- Täydellistä ratkaisua ei välttämättä tarvita, jos käyttötarkoitus on rajattu HTB-harjoitteluun  
  
## 11. Riskit / rajoitteet  
- Clipboard voi olla epäluotettava  
- Wayland-yhdistelmät käyttäytyvät eri tavalla compositorista riippuen  
- Ratkaisu ei välttämättä sovellu raskaaseen multimedia- tai pitkään GUI-työskentelyyn  
  
## 12. Jatkokehitys  
- Testaa NoMachine, jos SPICE ei riitä
- Vertaa tunnepohjaisesti SPICE vs NoMachine
- Dokumentoi lopullinen workflow HTB-käyttöön
- Laajenna labraa muihin VM:iin tarvittaessa
  
## 13. Mitä opin  
- Hypervisorin oma hallintakonsoli ei ole sama asia kuin oikea remote desktop-ratkaisu
- Wayland vaikuttaa käytännössä etäkäytön toteutukseen
- Ratkaisun valinnassa tärkeää on käyttötarkoitus, ei vain tekninen “paras mahdollinen”
- Dokumentointi auttaa sekä oppimisessa että osaamisen esittämisessä
  
## 14. Portfolio-kulma  
Tämä työ osoittaa käytännössä:  
- virtualisointiympäristön hallintaa  
- Linux-ympäristöjen käyttöä  
- etäkäyttöratkaisujen arviointia  
- teknistä ongelmanratkaisua  
- dokumentointikykyä  
  
## 15. Tiivistelmä työnantajalle  
Rakensin Proxmoxiin ParrotOS-pohjaisen harjoitteluympäristön eettistä hakkerointia ja tietoturvaharjoittelua varten, sekä arvioin käytännössä eri etäkäyttövaihtoehtoja Wayland-pohjaisessa Linux-ympäristössä. Valitsin SPICE-ratkaisun ensimmäiseksi toteutukseksi, koska se tarjosi parhaan kompromissin yksinkertaisuuden, toimivuuden ja käyttötarkoituksen välillä.