# Ekilex

Ekilex on Eesti Keele Instituudi infosüsteem, milles koostatakse ja hallatakse eesti keele sõnastikke ning terminikogusid. Süsteemi kasutavad EKI leksikograafid ja terminoloogid ning oma valdkonna eksperdid väljaspool EKI-t. Ekilexi sisu avaldatakse avalikult [Sõnaveebis](https://sonaveeb.ee).

See repositoorium sisaldab Ekilexi dokumentatsiooni.


```mermaid
  flowchart LR
      subgraph Kes["Kes koostab?"]
          A["EKI leksikograafid"]
          B["EKI terminoloogid"]
          C["EKI-välised oma valdkonna spetsialistid"]
      end

      subgraph Outer["Mida koostab?"]
          subgraph Ekilex["<b>Ekilex</b>"]
              D["EKI ühendsõnastik"]
              E["Esterm"]
              F["Teised terminikogud"]
          end
      end

      subgraph Avalik["Kus avaldatakse?"]
          subgraph WW["<b>Sõnaveeb</b>"]
              J["ÕS"]
              I["Keeleõppija Sõnaveeb"]
          end
      end

      A --> D
      B --> E
      C --> F
      D --> WW
      D -->|A2-B1 sõnavara| I
      D -->|raamat| J
      E --> WW
      F --> WW

      style Kes fill:#dbeafe,stroke:#93c5fd
      style Outer fill:#dbeafe,stroke:#93c5fd
      style Ekilex fill:#bfdbfe,stroke:#60a5fa
      style Avalik fill:#dbeafe,stroke:#93c5fd
      style WW fill:#bfdbfe,stroke:#60a5fa

      style A padding:10px
      style B padding:10px
      style C padding:10px
      style D padding:10px
      style E padding:10px
      style F padding:10px
      style I padding:10px
      style J padding:10px
``` 
