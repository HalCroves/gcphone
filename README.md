### gcphone par Jonathan (Gannon) D => https://github.com/N3MTV/gcphone

Update :
 * Réindetation du code pour une meilleur lisibilité 
 * Modification du commentaire pour l'ouverture du téléphone
 * Ajout d'auto messages
 * Modification des GetPlayerPed(-1) par PlayerPedId() dans 
    * client.lua
    * animation.lua
 * Dans photo.lua, modifications des touches pour utiliser la fonction ```local Keys = {```
 
 ---
 
### Reste à faire :
 * Le problème des selfies

--- 
### Tutorial :
/!\ For use this phone (Linux), you must absolutly use mysql-async 2.x !
 * https://github.com/brouznouf/fivem-mysql-async/tree/2.0
 
 ---
 
1. Download the gcphone and esx_addons_gcphone
2. Put the downloaded files on your ftp.
    * server-data/resources/
3. In, your server.cfg, put before the jobs addons :
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
