### gcphone par Jonathan (Gannon) D => https://github.com/N3MTV/gcphone

Update :
 * Réindetation du code pour une meilleur lisibilité 
 * Désactivation des print console pour les erreurs vendor.js
 * Modification du commentaire pour l'ouverture du téléphone
 * Ajout d'auto messages
 * Modification des GetPlayerPed(-1) par PlayerPedId() dans 
    * client.lua
    * animation.lua
 * Dans photo.lua, modifications des touches pour utiliser la fonction ```local Keys = {```
 * Possibilités de prendre des selfies => https://forum.fivem.net/t/release-cellphone-camera/43599
     * Flèche du haut pour rentrer en mode selfie
     * Clic gauche pour prendre une photo/selfie
     * Les screens sont enregistrés dans le répértoire `C:\Users\%USERNAME%\Documents\Rockstar Games\GTA V\Profiles\[inside one of folders idk which you'll have to guess]`
     * Possibilités de les ouvrir avec : https://www.gta5-mods.com/tools/gta5view-1-4-0dev4-exe/download/58989

Exemple : 
![Test Selfie](https://i.imgur.com/z7ZB2Xj.jpg)

A faire :
 * Ajouter des `Esx.ShowNotification` pour des messages d'aides (utilisation photo)
 
--- 
### Tutorial :
/!\ For use this phone (Linux), you must absolutly use mysql-async 2.x !
 * https://github.com/brouznouf/fivem-mysql-async/tree/2.0
 
 ---
 
1. Download the gcphone and esx_addons_gcphone
2. Put the downloaded files on your ftp.
    * server-data/resources/
3. In, your server.cfg, put **before** the jobs addons :
    * start gcphone
    * start esx_addons_gcphone

Like this : 
```
# you probably don't want to change these!
# only change them if you're using a server with multiple network interfaces

####################### PORT DU SERVEUR
endpoint_add_tcp "0.0.0.0:30120"
endpoint_add_udp "0.0.0.0:30120"

####################### CONFIG ES
set es_enableCustomData 1

####################### BASE DE DONNEES
set mysql_connection_string "server=localhost;database=essentialmode;userid=root;password=Saxophone21121993+"

####################### ES Base
start mysql-async
start essentialmode
start esplugin_mysql
start es_admin2 

####################### BASIC
start instance
start es_extended
start esx_voice
start esx_addons_gcphone
start gcphone

###### Jobs ######
start esx_service
start esx_mecanojob
start esx_lscustom
start esx_truckerjob
```

4. Configure the voice, in `gcphone/html/static/config/config.json`.
    * Basic use `"useWebRTCVocal": false,` unless you have a custom RTC server.
```lua
	"//": "useWebRTCVocal: false => Appels avec channels de GTA",
	"//": "useWebRTCVocal: true  => Appels avec WebRTC",
	"useWebRTCVocal": false,
	"RTCConfig": {
		"iceServers": [{
		"urls": ["turn:gannon.ovh"],
		"username": "jojo",
		"credential": "pass"
		}]
	},
```

5. Change the default zoom (**not tested**)

In `gcphone/html/static/config/config.json`, add this :  `"zoom" : "60%",`

Or in your `html/static/js/app.js` search  `zoom: window.localStorage.gc_zoom || "100%",` and replace by `zoom: window.localStorage.gc_zoom || "60%",`

Now, clear your server cache and maybe your FiveM cache.
     
---

For use distress signal (esx_ambulancejob), you need to edit the client.main :
Replace :
```lua
function SendDistressSignal()
	local playerPed = PlayerPedId()
	local coords	= GetEntityCoords(playerPed)

	ESX.ShowNotification(_U('distress_sent'))
	TriggerServerEvent('esx_phone:send', 'ambulance', _U('distress_message'), false, {
		x = coords.x,
		y = coords.y,
		z = coords.z
	})
end
```
By this : 
```lua
function SendDistressSignal()
	local playerPed = PlayerPedId()
	PedPosition		= GetEntityCoords(playerPed)
	
	local PlayerCoords = { x = PedPosition.x, y = PedPosition.y, z = PedPosition.z }

	ESX.ShowNotification(_U('distress_sent'))

    TriggerServerEvent('esx_addons_gcphone:startCall', 'ambulance', _U('distress_message'), PlayerCoords, {

		PlayerCoords = { x = PedPosition.x, y = PedPosition.y, z = PedPosition.z },
	})
end
```

---

For add custom message on phone :
```lua
			"display": "Police",
			"icon": "/html/static/img/icons_app/bank.png",
			"subMenu": [
				{
					"title": "Envoyer un message",
					"eventName": "esx_addons_gcphone:call",
					"type": {
						"number": "police"
					}
				},
				{
					"title": "Appeler le standard",
					"eventName": "gcphone:autoCallNumber",
					"type": {
						"number": "911"
					}
				},
				{
					"title": "Signaler un vol",
					"eventName": "esx_addons_gcphone:call",
					"type": {
						"number": "police",
						"message": "Vol en cours, merci de venir au plus vite !"
					}
				},
				{
					"title": "Signaler une agression",
					"eventName": "esx_addons_gcphone:call",
					"type": {
						"number": "police",
						"message": "Victime d'agression"
					}
				}
			]
```
