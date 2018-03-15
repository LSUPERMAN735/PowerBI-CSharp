# Power BI pour .NET

Bienvenue dans la communauté des développeurs .NET pour Power BI Embedded. Vous trouverez ici des ressources pour les SDK .NET pour Power BI Embedded

Pour toute question ou problème lié à l'utilisation des SDK, veuillez enregistrer un problème et nous vous répondrons dans les plus brefs délais.

Pour plus d'informations sur l'intégration à Power BI Embedded, consultez notre [Azure documentation](https://azure.microsoft.com/en-us/services/power-bi-embedded/).

## Issues
[Power BI Support Page](https://powerbi.microsoft.com/en-us/support/)

[Power BI Ideas](https://ideas.powerbi.com)

## Power BI Embedded Core Libraries
Le `Microsoft.PowerBI.Core` package contient des API pour générer des jetons d'intégration de rapport.

### Installer à partir des Nuget
`Install-Package Microsoft.PowerBI.Core`

### Usage: Création d'un rapport Intégrer un jeton
Power BI Embedded utilise des jetons d'intégration, qui sont des jetons Web JSON signés HMAC. Les jetons sont signés avec la clé d'accès de votre collection d'espace de travail Azure Power BI Embedded.
Les jetons intégrés sont utilisés pour fournir un accès en lecture seule à un rapport à incorporer dans une application.

Pour créer un jeton d'intégration de rapport, vous devez disposer d'une collection Azure Power BI Workspace, d'une clé d'accès, d'un ID d'espace de travail et d'un ID de rapport.

```
var accessKey = "{AzureAccessKey}";
var embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollection, workspaceId, reportId);
var jwt = embedToken.Generate(accessKey);

```

### Réclamations requises
- ver: 0.2.0
- typ: "embed"
- wcn: {WorkspaceCollectionName}
- wid: {WorkspaceId}
- rid: {ReportId} (or did: {DatasetId})
- aud: https://analysis.windows.net/powerbi/api
- exp: Token expiration in Unix EPOCH time

### Réclamations optionnelles
- nbp: Token valid not before in Unix EPOCH time
- username: The effective username to pass to Power BI Embedded for RLS (Row level security)
- roles: The roles to pass to Power BI Embedded for RLS
- scp: The permission scopes allowed with the designated Report or Dataset

Pour plus d'information sur [row level security](https://azure.microsoft.com/en-us/documentation/articles/power-bi-embedded-rls/) dans nos docs Azure.

### Exemple de jeton
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsIndjbiI6IlN1cHBvcnREZW1vIiwid2lkIjoiY2E2NzViMTktNmMzYy00MDAzLTg4MDgtMWM3ZGRjNmJkODA5IiwicmlkIjoiOTYyNDFmMGYtYWJhZS00ZWE5LWEwNjUtOTNiNDI4ZWRkYjE3IiwiaXNzIjoiUG93ZXJCSVNESyIsImF1ZCI6Imh0dHBzOi8vYW5hbHlzaXMud2luZG93cy5uZXQvcG93ZXJiaS9hcGkiLCJleHAiOjEzNjAwNDcwNTYsIm5iZiI6MTM2MDA0MzQ1Nn0.LgG2y0m24gg3vjQHhkXYYWKSVnGIUYT-ycA6JmTB6tg

## Ajout d'étendues d'autorisation aux jetons d'intégration
Lorsque vous utilisez des jetons Intégré, vous pouvez restreindre l'utilisation des ressources auxquelles il donne accès. Pour cette raison, vous pouvez générer un jeton avec des autorisations étendues.
```
var accessKey = "{AzureAccessKey}";
var scopes = {Scopes as string or array of strings};
var embedToken = PowerBIToken.CreateReportEmbedTokenWithScopes(workspaceCollection, workspaceId, reportId, scopes: scopes);
var jwt = embedToken.Generate(accessKey);

```
### Exemple de jeton - avec des scopes
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsIndjbiI6IlN1cHBvcnREZW1vIiwid2lkIjoiY2E2NzViMTktNmMzYy00MDAzLTg4MDgtMWM3ZGRjNmJkODA5IiwicmlkIjoiOTYyNDFmMGYtYWJhZS00ZWE5LWEwNjUtOTNiNDI4ZWRkYjE3Iiwic2NwIjoiUmVwb3J0LlJlYWQiLCJpc3MiOiJQb3dlckJJU0RLIiwiYXVkIjoiaHR0cHM6Ly9hbmFseXNpcy53aW5kb3dzLm5ldC9wb3dlcmJpL2FwaSIsImV4cCI6MTM2MDA0NzA1NiwibmJmIjoxMzYwMDQzNDU2fQ.M1jkWXnkfwJeGQqh1x0vIAYB4EBKbHSZFoDB6n_LZyA

#### Décodé
Le jeton JSON Web décodé est le suivant
**Header**
```javascript
{
  "typ": "JWT",
  "alg": "HS256"
}
```

**Payload**
```javascript
{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}
```

## Client REST Power BI intégré
Le `Microsoft.PowerBI.Api` est un client .NET REST pour consommer facilement les services REST Power BI intégré.

### Installer à partir de Nuget
`Install-Package Microsoft.PowerBI.Api`

### Usage: Calling the GetReports API
As an example, to get a list or reports within your workspace you need to instantiate a `PowerBIClient` with credentials and call into the `GetReports` API.
```
var credentials = new TokenCredentials("{AzureAccessKey}", "AppKey");
var client = new PowerBIClient(credentials);

var reportsResult = await client.Reports.GetReportsAsync(workspaceCollection, workspaceId);

```

The following APIs groups are available:

- Datasets
- Gateways
- Imports
- Reports
- Workspaces

## Power BI Embedded for JavaScript
The JavaScript SDK is underlying component for all embed scenarios.  The SDK is vanilla JS but we also ship components for many popular SPA frameworks including Angular, React & Ember JS.  

Visit our [JavaScript SDK](https://github.com/Microsoft/powerbi-javascript) home for more information

### Installer à partir de Nuget
`Install-Package Microsoft.PowerBI.JavaScript`

### Setup Power BI for embedding
Add the Power BI script include before your apps closing `</body>` tag

*You can optionally add the CSS reference to an ASP.NET style bundle*

`<script src="/scripts/powerbi.js"></script>`

### Définition de la taille des composants incorporés
La mosaïque et l'intégration de rapport seront automatiquement intégrées en fonction de la taille du conteneur d'intégration.
Pour remplacer la taille par défaut des embeds, ajoutez simplement un attribut de classe CSS ou des styles en ligne pour width & height.

# ASP.NET MVC
The `Microsoft.PowerBI.AspNet.Mvc` package is a lightweight wrapper that contains MVC HTML helpers that generate HTML markup compatible with the core JavaScript SDK.

## Installer à partir de Nuget
`Install-Package Microsoft.PowerBI.AspNet.Mvc`

## Configurez votre jeton d'accès
Générez votre jeton d'accès d'intégration de rapport avec le `Microsoft.PowerBI.Core` API de jetons.
`@Html.PowerBIAccesstoken({{YourAccesstoken}})`

## Intégrez votre rapport
`@Html.PowerBIReportFor(m => m.EmbedUrl)`

# ASP.NET WebForms
Le `Microsoft.PowerBI.AspNet.WebForms` package est un wrapper léger qui contient des contrôles ASP.NET Webform qui génèrent un balisage HTML compatible avec le SDK JavaScript principal.
## Installer à partir de Nuget
`Install-Package Microsoft.PowerBI.AspNet.WebForms`

## Configurez votre jeton d'accès
Assurez-vous d'avoir les éléments suivants dans votre vue

`<powerbi:Token ID="pbiAccessToken" runat="server" />`

Si vous gérez vous-même des jetons d'accès, assurez-vous de les indiquer ici

`<powerbi:Token ID="pbiAccessToken" AccessToken="{{YourAccessToken}}" runat="server" />`

## Intégrer un rapport

`<powerbi:Report id="pbiReport" EmbedUrl="{{EmbedUrl}}" runat="server" />`
