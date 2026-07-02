FloodForm Developer

A flood-adjusted land valuation tool for Houston-area infill developers.

Click a parcel and get live FEMA / USGS / NWS flood intelligence, HCAD lot identification, a flood-resilience construction premium, a Risk Rating 2.0–style insurance estimate, and a maximum-land-offer waterfall with a Go / Negotiate / Redesign / Avoid verdict. Single-file HTML, no build step.


Most tools tell a developer whether a lot floods. FloodForm tells them whether the deal still pencils once flood exposure is priced into construction cost, insurance cost, and the residual land value.




What it does

FloodForm Developer turns a Houston–Beaumont parcel into a buy decision. Enter an address or click anywhere on the map, and the tool:


Resolves the location to a street address and identifies the underlying HCAD parcel (boundary, ID, size, owner).
Pulls the effective FEMA flood zone and base flood elevation, USGS ground elevation and nearest stream-gauge level, and active NWS flood alerts.
Scores site resilience (0–100) from zone, freeboard, elevation, gauge level, and alerts.
Estimates a property-level flood-insurance premium (RR 2.0 proxy) and flags whether coverage is mandatory for federally backed financing.
Models an effective-vs-draft (MAAPnext) outlook and warns when a parcel that's safe today would be pulled into a high-risk zone — triggering a future mandatory-insurance requirement on adoption.
Runs a residual land-value waterfall — separating the flood-resilience build cost from the insurance value drag — to produce a maximum land offer and a decisive verdict.
Writes a plain-English deal brief and exports a branded one-page PDF leave-behind.


Everything runs client-side against live public APIs. No server, no build pipeline, no API keys required for the core analysis.


Features


Click-to-identify lot selection against the live HCAD parcel service — auto-fills the real lot size into the valuation.
Live flood intelligence: FEMA NFHL zones + base flood elevation (with a fallback query to the riverine BFE line layer), USGS 3DEP ground elevation, USGS NWIS stream gauges, NWS active alerts.
Resilience score blending zone, freeboard, elevation, and current conditions.
RR 2.0–style insurance estimate driven by first-floor height above BFE and replacement cost — not just the zone line — plus a lender-requirement flag.
Effective vs. draft (MAAPnext) outlook with a forward-looking mandatory-insurance trigger flag.
Flood-resilience construction premium that scales with required structural elevation and rewards lots already above BFE.
Buildable-capacity model (FAR, efficiency, unit yield) across five Houston product types: townhouse, duplex, small multifamily, mixed-use, single-family.
Maximum-land-offer waterfall with editable assumptions (FAR, hard cost, soft cost, profit target, cap rate).
Go / Negotiate / Redesign / Avoid verdict with the offer-vs-ask spread.
AI deal brief via the Anthropic API, with a rule-based fallback when unavailable.
One-page PDF export (jsPDF, with a print fallback) — a light-themed, branded report for leave-behinds.
Three-tier geocoding (Census JSONP → Nominatim → Photon) and reverse geocoding, tuned to be CORS-safe.



Getting started

No install, no build. Just open the file:

bash# clone, then open in a browser
git clone https://github.com/<you>/floodform-developer.git
cd floodform-developer
open floodform-developer.html      # macOS  (use: xdg-open on Linux, start on Windows)

Or host the single file anywhere static (GitHub Pages, Netlify, S3) and visit the URL.


Tip: open the file in a full browser tab rather than an embedded preview — some sandboxed frames block cross-origin API calls, pop-ups, and downloads.




Data sources

LayerSourceNotesFlood zones & BFEFEMA NFHL (hazards.fema.gov)Zone fills (layer 28); BFE from the polygon or the riverine line layer (16)Ground elevationUSGS EPQS (epqs.nationalmap.gov)3DEP point elevation, feetStream gaugesUSGS NWIS (waterservices.usgs.gov)Nearest active gage heightFlood alertsNWS (api.weather.gov)Active alerts filtered to flood eventsParcelsHCAD (gis.hctx.net)Harris County only — boundary, ID, area, ownerGeocodingUS Census (JSONP), Nominatim, PhotonForward + reverseBasemapCartoDB dark, OpenStreetMap fallbackLeaflet


How the valuation works

The core is a residual land-value model. Whatever remains after every cost and the required profit is the most a developer can pay for the dirt:

Maximum Land Offer =  Stabilized Value
                    − Hard Construction
                    − Soft Cost
                    − Flood-Resilience Premium   (build cost, from flood data)
                    − Flood-Insurance Impact     (RR 2.0 proxy → value drag)
                    − Required Developer Profit
                    − Risk Buffer                (scales with resilience score)

A negative maximum land offer means the deal is underwater before land is even priced — even free land loses money — which returns AVOID. The two flood costs are broken out as separate lines so you can see whether it's the build premium or the insurance drag doing the damage, and whether a better-elevated lot would recover the deal.

Figures are planning-grade Houston defaults and every assumption is editable.


Configuration

A few hooks are exposed as constants near the top of the script:


HCAD_PARCELS — the parcel query endpoint. Covers Harris County; add other appraisal-district endpoints (Fort Bend, Montgomery, Jefferson) to extend coverage toward Beaumont.
DRAFT_FLOODPLAIN_QUERY — set to a function(lat, lng) → zoneString pointing at a verified MAAPnext ArcGIS endpoint to switch the draft outlook from modeled to live. Until then it shows a clearly labeled modeled scenario and links to the official viewer.
Deal brief — calls the Anthropic Messages API. Self-hosted deployments need an API key or a proxy for the live AI brief; without one, the tool falls back to a rule-based brief automatically.



Limitations & disclaimers


Estimates only. Effective flood data is FEMA NFHL. The draft outlook is a modeled scenario unless a live MAAPnext endpoint is configured — verify drafts at hcfcd.org/MAAPnext.
Insurance figures are a Risk Rating 2.0–style proxy, not a quote. Under RR 2.0, flood maps set the requirement to carry coverage, not the premium — confirm actual cost with an NFIP or private carrier.
Cost, value, capacity, and offer figures are planning-grade defaults for the Houston–Beaumont market. Confirm zoning/deed restrictions, comps, engineered flood-resilience scope, and financing terms before committing capital.
Parcel identification is Harris County only as configured.
Not lending, legal, insurance, or investment advice.



Roadmap


Verify and wire the live MAAPnext draft-floodplain endpoint.
Extend parcel coverage to Fort Bend, Montgomery, and Jefferson counties.
Sharpen the insurance-premium model (distance-to-water, foundation type, prior claims).
Validate with customer conversations before further feature work — the PDF one-pager is the primary validation artifact.



Tech

Single-file HTML. Leaflet for mapping, jsPDF for export, live public REST/ArcGIS services for data. No framework, no build step.
