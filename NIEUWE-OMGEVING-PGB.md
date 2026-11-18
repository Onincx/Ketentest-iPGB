# Ketentestmonitor PGB — in gebruik nemen (stappenplan)

Deze map is een kopie van de ketentestmonitor met de naam **Ketentestmonitor PGB**
(alle zichtbare verwijzingen naar "Bemiddelingsregister" zijn vervangen).
De PGB-versie gebruikt **dezelfde Supabase-database** als de bestaande versie:
dezelfde gebruikers en organisaties, maar de PGB-ketentest zet je als aparte
ketentest in Beheer. Er is dus géén nieuw SQL-script nodig.

Voorgestelde URL: `https://ketentestmonitor-pgb.vercel.app`
(is die bezet? Kies een andere naam en pas hem aan in stap 4 én in de twee
bestanden onder `supabase/functions/`, zie stap 5.)

## Stap 1 — Nieuwe GitHub-repository
1. Maak (eventueel in een nieuw GitHub-account) een nieuwe lege repository, bijv. `Ketentestmonitor-PGB`.
2. Upload de inhoud van deze map (niet de map zelf) naar die repository:
   "Add file → Upload files", sleep alle bestanden en mappen erin, "Commit changes".

## Stap 2 — Nieuw Vercel-account en project
1. Maak het nieuwe (gratis) Vercel-account aan en koppel het aan het GitHub-account met de nieuwe repository.
2. "Add New… → Project" → kies de repository → Framework Preset: **Other** → alles verder op standaard → **Deploy**.
3. Er is geen build-opdracht en geen environment variable nodig (de Supabase-sleutels staan in `js/supabase-config.js`).

## Stap 3 — Projectnaam / URL kiezen
Vercel → Project → Settings → Domains: voeg `ketentestmonitor-pgb.vercel.app` toe
(of hernoem het project in Settings → General → Project Name).

## Stap 4 — Supabase: nieuwe URL toestaan
Supabase Dashboard → Authentication → URL Configuration:
- **Redirect URLs**: voeg `https://ketentestmonitor-pgb.vercel.app/**` toe
  (de bestaande URL laat je staan, anders werkt de oude site niet meer).
- **Site URL** laat je op de huidige waarde staan.

## Stap 5 — Edge functions bijwerken (wachtwoord-reset en inlognaam-migratie)
Beide functies staan alleen aanroepen toe vanaf bekende URL's. De nieuwe URL is
al toegevoegd in:
- `supabase/functions/reset-user-password/index.ts`
- `supabase/functions/migrate-to-username-login/index.ts`

Deploy beide opnieuw naar Supabase (Dashboard → Edge Functions → de functie
openen → code vervangen door de inhoud van het bestand → Deploy; of via de CLI
`supabase functions deploy reset-user-password` en `... migrate-to-username-login`).
Zonder deze stap werkt "Wachtwoord resetten" vanaf de PGB-site niet.

## Stap 6 — PGB-ketentest aanmaken
1. Log in op de nieuwe URL met je bestaande beheerdersaccount.
2. Beheer → Ketentesten → nieuwe ketentest "PGB" toevoegen.
3. Beheer → Organisaties: koppel de deelnemende organisaties aan de PGB-ketentest.
4. Maak scenario's, kenmerken en flows aan (kenmerk-groepen kun je per ketentest koppelen).

## Let op
- Dezelfde database betekent dat gebruikers van beide sites dezelfde accounts
  hebben, en dat beheerders op beide sites alle ketentesten kunnen zien. Wil je
  de gegevens strikt gescheiden houden, dan is een eigen Supabase-project nodig
  (nieuw project + alle scripts in `sql/` opnieuw draaien + nieuwe sleutels in
  `js/supabase-config.js` en `vercel.json`).
- De oude URL blijft gewoon werken.
