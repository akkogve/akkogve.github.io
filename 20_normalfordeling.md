# Innleiing

Vi går i heile avsnittet ut frå at $X$ er ein *normalfordelt* stokastisk variabel med parametre *forventing* $\mu$ (variabelen `mu` i Scilab) og *standardavvik* $\sigma$ (variabelen `sigma` i Scilab).

## Grunnleggande sannsynligheter

Den mest grunnleggande sannsynligheten, $P(X<a)$ rekner vi ut med kommandoen 

    G(a, mu, sigma)

som ligg i eit skript. Tilsvarande rekner vi ut $P(X>b)$ som

    1 - G(b, mu, sigma)
    
og den doble sannsynligheten $P(a<X<b)$ som

    G(b, mu, sigma) - G(a, mu, sigma)
    
## Spredningsintervall

Ein viktig ting ved normalfordelingar er *intervall* som definerer ein viss sannsynlighet. Vi er til dømes på jakt etter eit intervall $[a, b]$ slik at det er 95 % sannsynlighet for at $X$  havner i dette intervallet: $P(a<X<b)=0,95$.
Vi definerer $a$ og $b$ slik at 
$$a = \mu - z_{0,025}\cdot \sigma \qquad \text{og} \qquad b = \mu + z_{0,025}\cdot \sigma$$
Her er $z_{0,025}$ ein faktor som fortel *kor mange standardavvik* vi må gå ut frå $\mu$ for å oppnå den ønska sannsynligheten.  Meir generelt er formelen slik at 
$$P(a<X<b) = 1-\alpha$$
dersom
$$a = \mu - z_{\alpha/2}\cdot \sigma \qquad \text{og} \qquad b = \mu + z_{\alpha/2}\cdot \sigma$$


Vi kan bruke det ferdig definerte skriptet `za(alpha)` til å finne $z_\alpha$:

    Z = za(0.025)  // gir kvantilen for 2.5 % på kvar side.
    
**Døme:** Dersom $\mu=123,456$ og $\sigma = 0,005$ er eit 95 %-intervall definert ved

    a = mu - za(0.025)*sigma  // gir 123.4462
    b = mu + za(0.025)*sigma  // gir 123.4658
    
## Fordelinga til gjennomsnittet

Som vi såg i estimeringsdelen er det ikkje enkeltobservasjonar vi bruker mest tid på, det er *gjennomsnitt* av mange. No kan det visast at dersom ein enkelt observasjon $X$ er normalfordelt: $X\sim N(\mu, \sigma)$ så er også gjennomsnittet normalfordelt:
$$\overline{X}\sim N\left(\mu, \frac{\sigma}{\sqrt{n}}\right)$$
Det betyr at vi like godt kan lage spredningsintervall for gjennomsnittet (men no heiter dei *konfidensintervall*):
$$P(a<\overline{X}<b)$$
dersom
$$a = \mu - z_{\alpha/2}\cdot \frac{\sigma}{\sqrt{n}} \qquad \text{og} \qquad b = \mu + z_{\alpha/2}\cdot \frac{\sigma}{\sqrt{n}}$$
Når vi har estimert forventingsverdien ved å ta gjennomsnittet kan vi altså lage eit konfidensintervall som seier at "vi er 95 % sikre på at den sanne (ukjende) verdien av $\mu$ ligg inne i dette intervallet." I andre tilfeller kan det vere aktuelt med 99 % sikkerhet eller endå meir, men 95 % er svært vanleg.

## Estimering av $\sigma$: *t*-fordeling

Kva gjer vi dersom vi *ikkje* kjenner usikkerheten $\sigma$? Vi må estimere denne også, og vi må lage intervall rundt $\overline{X}$ på nytt (dessverre).

Estimatoren er det empiriske standardavviket som vi i Scilab finn ved `stdev(data)`:

    S = stdev(data)
    
Det "nye" intervallet vi får vert no veldig likt det gamle:
$$P(a<\overline{X}<b)$$
der
$$a = \mu - t_{\alpha/2}\cdot \frac{S}{\sqrt{n}} \qquad \text{og} \qquad b = \mu + t_{\alpha/2}\cdot \frac{S}{\sqrt{n}}$$
men vi har bytta ut kvantilen $z_{\alpha/2}$ med $t_{\alpha/2}$, og parameteren $\sigma$ med estimatoren $S$. Vi har eit skript for å finne $t_{\alpha/2}$ også:

    t = ta(alpha, n)

der $n$ er talet på observasjonar. Dersom vi har eit datasett med $n=10$ observasjonar vil 2,5 % kvantilen altså vere

    t = ta(0.025, 10)  // gir 2.2621572
    
**Døme:** I eit datasett på $n=5$ observasjonar har vi estimert $\overline{X} = 123,456$ og $S = 0.008$. Finn eit 95 % intervall for forventingsverdien $\mu$:

    a = mu - ta(0.025, 5)*S/sqrt(5) // gir 123.44607
    b = mu + ta(0.025, 5)*S/sqrt(5) // gir 123.46593
    
## Intervall rundt $S$

Når $S$ er estimert på bakgrunn av data så må vi kunne lage oss eit intervall rundt den også. Men dette intervallet har ein eigenskap som ikkje intervallet rundt $\overline{X}$ har: det er ikkje symmetrisk. Grunnen til det kjem av at $S$ per definisjon er eit *positivt* tal (det er kvadratrota av eit kvadrattal), og dermed kan det ikkje vere symmetrisk. I staden følger det ein distribusjon som ser omtrenbt ut so på figuren nedanfor:

![Chikvadrat-fordelinga](chi-square-dist2.gif)

Som vi ser så er ikkje dei to kvantilane symmetriske, og vi må lage eit intervall som har to ulike kvantilar:
$$P(a<S<b)$$
dersom
$$a = \frac{(n-1)\cdot S^2}{\chi_{\alpha/2}} \qquad \text{og} \qquad b = \frac{(n-1)\cdot S^2}{\chi_{1-\alpha/2}}$$
Dei to kvantilane $\chi_{\alpha/2}$ og $\chi_{1-\alpha/2}$ finn vi ved skriptet `Xa` i Scilab:

    Xa(alpha, n)

**Døme:** Basert på $n=10$ observasjonar er standardavviket til eit måleapparat estimert til $S=0,\!008$ m. Sett opp eit 95 % intervall for den sanne (og ukjende) usikkerheten $\sigma $.

Vi finn dei to kvantilane som 

    X1 = Xa(0.025, 10) // gir 19.022768
    X2 = Xa(0.975, 10) // gir 2.7003895
    
og dermed vert dei to grensene (for $S^2$)

    a2 = 9*S^2/X1 // gir 0.0000303
    b2 = 9*S^2/X2 // gir 0.0002133
    
og grensene for $S$ vert då

    a = sqrt(a2) // gir 0.0055027
    b = sqrt(b2) // gir 0.0146049
    
Så ein eller annan stad i intervallet $[0,\!006,\;0,\!015]$ ligg den sanne verdien av $\sigma$.
