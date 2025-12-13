#### TF\_Commande

let

&nbsp;   // 📊 PARTIE EXCEL (ACCESS)

&nbsp;   Excel\_Fait = let

&nbsp;       Source = Orders\_excel,

&nbsp;       RenameCols = Table.RenameColumns(Source,{

&nbsp;           {"Customer ID","id\_client\_prod"},

&nbsp;           {"Employee ID","id\_employee\_prod"},

&nbsp;           {"Order Date","DateCommande"},

&nbsp;           {"Shipped Date","DateLivraison"}

&nbsp;       }),

&nbsp;       TypeFix = Table.TransformColumnTypes(RenameCols, {{"DateCommande",type date},{"DateLivraison",type date}}),

&nbsp;       KeepCols = Table.SelectColumns(TypeFix, {"id\_client\_prod","id\_employee\_prod","DateCommande","DateLivraison"}),

&nbsp;       AddSource = Table.AddColumn(KeepCols,"source\_prod",each "ACCESS"),

&nbsp;       

&nbsp;       //  Créer mois\_annee pour la jointure

&nbsp;       AddMonthYear = Table.AddColumn(AddSource, "mois\_annee", each Text.PadStart(Text.From(Date.Month(\[DateCommande])),2,"0") \& "-" \& Text.From(Date.Year(\[DateCommande])), type text),

&nbsp;       

&nbsp;       //  Jointure sur mois\_annee au lieu de DateComplete

&nbsp;       MergeTemps = Table.NestedJoin(AddMonthYear, {"mois\_annee"}, Dim\_Temps, {"mois\_annee"}, "Temps", JoinKind.LeftOuter),

&nbsp;       ExpandTemps = Table.ExpandTableColumn(MergeTemps, "Temps", {"id\_temps"}, {"id\_temps"}),

&nbsp;       

&nbsp;       MergeEmp = Table.NestedJoin(

&nbsp;           ExpandTemps, 

&nbsp;           {"id\_employee\_prod","source\_prod"}, 

&nbsp;           Table.Distinct(Dim\_Employee, {"id\_employee\_prod","source\_prod"}), 

&nbsp;           {"id\_employee\_prod","source\_prod"}, 

&nbsp;           "Emp", 

&nbsp;           JoinKind.LeftOuter

&nbsp;       ),

&nbsp;       ExpandEmp = Table.ExpandTableColumn(MergeEmp, "Emp", {"id\_seqEmployee"}, {"id\_seqEmployee"}),

&nbsp;       

&nbsp;       MergeClient = Table.NestedJoin(ExpandEmp, {"id\_client\_prod","source\_prod"}, Dim\_Client, {"id\_client\_prod","source\_prod"}, "Client", JoinKind.LeftOuter),

&nbsp;       ExpandClient = Table.ExpandTableColumn(MergeClient, "Client", {"id\_seqClient"}, {"id\_seqClient"}),

&nbsp;       

&nbsp;       AddDelivered = Table.AddColumn(ExpandClient,"nbr\_commande\_livrees", each if \[DateLivraison]<>null then 1 else 0, Int64.Type),

&nbsp;       AddNotDelivered = Table.AddColumn(AddDelivered,"nbr\_commande\_non\_livrees", each if \[DateLivraison]=null then 1 else 0, Int64.Type),

&nbsp;       

&nbsp;       //  Supprimer aussi mois\_annee dans le nettoyage

&nbsp;       Cleanup = Table.RemoveColumns(AddNotDelivered, {"id\_client\_prod","id\_employee\_prod","DateCommande","DateLivraison","source\_prod","mois\_annee"})

&nbsp;   in

&nbsp;       Cleanup,



&nbsp;   //  PARTIE SSMS

&nbsp;   SSMS\_Fait = let

&nbsp;       Source = Orders\_ssms,

&nbsp;       RenameCols = Table.RenameColumns(Source,{

&nbsp;           {"CustomerID","id\_client\_prod"},

&nbsp;           {"EmployeeID","id\_employee\_prod"},

&nbsp;           {"OrderDate","DateCommande"},

&nbsp;           {"ShippedDate","DateLivraison"}

&nbsp;       }),

&nbsp;       TypeFix = Table.TransformColumnTypes(RenameCols, {

&nbsp;           {"DateCommande", type date},

&nbsp;           {"DateLivraison", type date}

&nbsp;       }),

&nbsp;       CleanDate = Table.TransformColumns(TypeFix, {

&nbsp;           {"DateCommande", Date.From, type date}

&nbsp;       }),

&nbsp;       KeepCols = Table.SelectColumns(CleanDate, {"id\_client\_prod","id\_employee\_prod","DateCommande","DateLivraison"}),

&nbsp;       AddSource = Table.AddColumn(KeepCols,"source\_prod",each "SSMS"),

&nbsp;       

&nbsp;       //  Créer mois\_annee pour la jointure

&nbsp;       AddMonthYear = Table.AddColumn(AddSource, "mois\_annee", each Text.PadStart(Text.From(Date.Month(\[DateCommande])),2,"0") \& "-" \& Text.From(Date.Year(\[DateCommande])), type text),

&nbsp;       

&nbsp;       //  Jointure sur mois\_annee au lieu de DateComplete

&nbsp;       MergeTemps = Table.NestedJoin(AddMonthYear, {"mois\_annee"}, Dim\_Temps, {"mois\_annee"}, "Temps", JoinKind.LeftOuter),

&nbsp;       ExpandTemps = Table.ExpandTableColumn(MergeTemps, "Temps", {"id\_temps"}, {"id\_temps"}),

&nbsp;       

&nbsp;       MergeEmp = Table.NestedJoin(

&nbsp;           ExpandTemps, 

&nbsp;           {"id\_employee\_prod","source\_prod"}, 

&nbsp;           Table.Distinct(Dim\_Employee, {"id\_employee\_prod","source\_prod"}), 

&nbsp;           {"id\_employee\_prod","source\_prod"}, 

&nbsp;           "Emp", 

&nbsp;           JoinKind.LeftOuter

&nbsp;       ),

&nbsp;       ExpandEmp = Table.ExpandTableColumn(MergeEmp, "Emp", {"id\_seqEmployee"}, {"id\_seqEmployee"}),

&nbsp;       

&nbsp;       MergeClient = Table.NestedJoin(ExpandEmp, {"id\_client\_prod","source\_prod"}, Dim\_Client, {"id\_client\_prod","source\_prod"}, "Client", JoinKind.LeftOuter),

&nbsp;       ExpandClient = Table.ExpandTableColumn(MergeClient, "Client", {"id\_seqClient"}, {"id\_seqClient"}),

&nbsp;       

&nbsp;       AddDelivered = Table.AddColumn(ExpandClient,"nbr\_commande\_livrees", each if \[DateLivraison]<>null then 1 else 0, Int64.Type),

&nbsp;       AddNotDelivered = Table.AddColumn(AddDelivered,"nbr\_commande\_non\_livrees", each if \[DateLivraison]=null then 1 else 0, Int64.Type),

&nbsp;       

&nbsp;       //  Supprimer aussi mois\_annee dans le nettoyage

&nbsp;       Cleanup = Table.RemoveColumns(AddNotDelivered, {"id\_client\_prod","id\_employee\_prod","DateCommande","DateLivraison","source\_prod","mois\_annee"})

&nbsp;   in

&nbsp;       Cleanup,



&nbsp;   //  UNION DES DEUX SOURCES

&nbsp;   Combined = Table.Combine({Excel\_Fait, SSMS\_Fait}),

&nbsp;   

&nbsp;   //  Ajout ID séquentiel pour la table de faits unifiée

&nbsp;   #"Added Index" = Table.AddIndexColumn(Combined, "id\_seq\_fait", 1, 1, Int64.Type),

&nbsp;   

&nbsp;   //  Réorganisation finale

&nbsp;   #"Reordered Columns" = Table.ReorderColumns(#"Added Index",{"id\_seq\_fait","id\_temps","id\_seqEmployee","id\_seqClient","nbr\_commande\_livrees","nbr\_commande\_non\_livrees"})

in

&nbsp;   #"Reordered Columns"

