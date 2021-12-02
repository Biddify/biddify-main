# OAuth2 Research

## Wat is OAuth2?
![High level overview oauth2](https://i.ibb.co/3MgdzL5/21-12-01-RR-High-level-overview-oauth2.png)

Allereerst kijken we naar het "high level overview" van OAuth2. Om dit te versimpelen gebruiken we 3 rollen. 
We hebben een user, applicatie & api. Binnen de api is een authentication server en een resource server. 
Het verschil tussen deze twee behandelen we later. 
Met de groeiende populariteit van OAuth2 heeft u vast ooit mee te maken gehad. 

![High level overview oauth2 logos](https://i.ibb.co/xDTcQtS/21-12-01-RR-High-level-overview-oauth2-logos.png)

Bijvoorbeeld als u Spotify start om naar u favoriete muziek te luisteren heeft u er misschien voor kozen om in te loggen met Facebook. 
Op dat moment vraagt Spotify aan facebook uw basis informatie en profiel foto. 
In dit voorbeeld is Spotify de applicatie en Facebook de api. 

Wanneer ik inlog bij Spotify met mijn Facebook account, 
gebruikt Spotify mijn gebruikersnaam en wachtwoord van Facebook. 
Als u nu denkt dus zo werkt OAuth2, dit is fout. 
Dit is een van de grootste misvattingen van het OAuth2 framework wachtwoorden worden nooit meegestuurd in het process.
Maar hoe werkt het dan? 

Laten we Biddify als een voorbeeld nemen. Jan is een gebruiker hij wil graag een product kopen.
Hiervoor moet hij zijn bankrekening koppelen met Biddify. 

## Implicit grant flow
![High level overview oauth2 logos](https://i.ibb.co/FVCZjGc/21-12-01-RR-Implicit-grant-overview.png)

De implicit flow is de makkelijkste maar ook meteen de onveiligste OAuth2 grant. Bij de implicit flow verstuurd de browser een authorize GET request naar de server met een `response_type=token`.
Dit geeft door naar de server dat de client graag een token terug ontvangt op de request. 
In de response die de client terug ontvangt van de server zit een fragment wat er als volgt uitziet `access_token=anAccessToken`. 
Het is belangrijk om te weten dat het fragment altijd binnen de browser blijft. En meestal in de Javascript wordt opgehaald.  

Voordelen:

- Minder requesten om een access_token op te halen. 
- Makkelijker om te implementeren. 

Nadelen:
- Onveilig, alle requesten zijn public.
- Access_token kan door derden worden ontvangen. 

De implicit flow slaat een belangrijke stap over, namelijk het veilig ophalen van het token van de OAuth2 server. 
Deze stap ontstaat wanneer de applicatie back-end een request maakt naar het token endpoint om het token op te halen. 

Anders dan bij de authorization grant, stuurt de implicit grant de browser niet terug naar de applicatie back-end met een authorization code. 
In plaats daarvan plaats hij het access token direct in url als onderdeel van het omleiden. 

Een voorbeeld van de url ziet er als volgt uit:`https://website.nl/#access_token=anAccessToken`