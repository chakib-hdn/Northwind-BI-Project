#### Dim\_Temps

let

&nbsp;   // Partie SSMS -  FORCER LE TYPE DATE

&nbsp;   Temps\_SSMS = let

&nbsp;       Source = Orders\_ssms,

&nbsp;       #"Selected Columns" = Table.SelectColumns(Source, {"OrderDate"}),

&nbsp;       #"Clean Date" = Table.TransformColumns(#"Selected Columns", {{"OrderDate", Date.From, type date}}),

&nbsp;       #"Renamed Columns" = Table.RenameColumns(#"Clean Date", {{"OrderDate", "DateComplete"}})

&nbsp;   in

&nbsp;       #"Renamed Columns",

&nbsp;   

&nbsp;   // Partie Excel

&nbsp;   Temps\_ACCESS = let

&nbsp;       Source = Orders\_excel,

&nbsp;       #"Selected Columns" = Table.SelectColumns(Source, {"Order Date"}),

&nbsp;       #"Changed Type" = Table.TransformColumnTypes(#"Selected Columns", {{"Order Date", type date}}),

&nbsp;       #"Renamed Columns" = Table.RenameColumns(#"Changed Type", {{"Order Date", "DateComplete"}})

&nbsp;   in

&nbsp;       #"Renamed Columns",

&nbsp;   

&nbsp;   // Union des deux sources

&nbsp;   Unioned = Table.Combine({Temps\_SSMS, Temps\_ACCESS}),

&nbsp;   

&nbsp;   //  FORCER LE TYPE DATE APRÈS UNION

&nbsp;   ForceDate = Table.TransformColumns(Unioned, {{"DateComplete", Date.From, type date}}),

&nbsp;   

&nbsp;   // Ajout de l'année et mois\_année AVANT de faire le distinct

&nbsp;   AddYear = Table.AddColumn(ForceDate, "annee", each Date.Year(\[DateComplete]), Int64.Type),

&nbsp;   AddMonthYear = Table.AddColumn(AddYear, "mois\_annee", each Text.PadStart(Text.From(Date.Month(\[DateComplete])),2,"0") \& "-" \& Text.From(Date.Year(\[DateComplete])), type text),

&nbsp;   

&nbsp;   //  DISTINCT sur mois\_annee uniquement (garde la première date de chaque mois)

&nbsp;   UniqueMonths = Table.Distinct(AddMonthYear, {"mois\_annee"}),

&nbsp;   

&nbsp;   // Supprimer la colonne DateComplete car elle n'est plus pertinente (on a plusieurs dates par mois)

&nbsp;   RemoveDate = Table.RemoveColumns(UniqueMonths, {"DateComplete"}),

&nbsp;   

&nbsp;   // Ajout de l'ID séquentiel

&nbsp;   #"Added Index" = Table.AddIndexColumn(RemoveDate, "id\_temps", 1, 1, Int64.Type),

&nbsp;   

&nbsp;   // Réorganisation finale

&nbsp;   #"Reordered Columns" = Table.ReorderColumns(#"Added Index",{"id\_temps","annee","mois\_annee"})

in

&nbsp;   #"Reordered Columns"

