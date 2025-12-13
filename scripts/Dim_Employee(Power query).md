#### Dim\_Employee

let

&nbsp;   // SSMS Preparation (votre code est parfait)

&nbsp;   SSMS\_Prepared = let

&nbsp;       Source = Employee\_ssms,

&nbsp;       JoinEmpTerritories = Table.NestedJoin(Source, {"EmployeeID"}, EmployeeTerritories, {"EmployeeID"}, "EmpTerritories", JoinKind.LeftOuter),

&nbsp;       ExpandEmpTerritories = Table.ExpandTableColumn(JoinEmpTerritories, "EmpTerritories", {"TerritoryID"}, {"TerritoryID"}),

&nbsp;       JoinTerritories = Table.NestedJoin(ExpandEmpTerritories, {"TerritoryID"}, Territories, {"TerritoryID"}, "Territories", JoinKind.LeftOuter),

&nbsp;       ExpandTerritories = Table.ExpandTableColumn(JoinTerritories, "Territories", {"TerritoryDescription", "RegionID"}, {"TerritoryDescription", "RegionID"}),

&nbsp;       AddSource = Table.AddColumn(ExpandTerritories, "source\_prod", each "SSMS"),

&nbsp;       SelectColumns = Table.SelectColumns(AddSource, {"EmployeeID", "LastName", "FirstName", "TerritoryID", "TerritoryDescription", "RegionID", "source\_prod"})

&nbsp;   in

&nbsp;       SelectColumns,



&nbsp;   // Excel Preparation (votre code est parfait)  

&nbsp;   Excel\_Prepared = let

&nbsp;       Source = Employee\_excel,

&nbsp;       RenameColumns = Table.RenameColumns(Source,{

&nbsp;           {"ID", "EmployeeID"},

&nbsp;           {"Last Name", "LastName"}, 

&nbsp;           {"First Name", "FirstName"},

&nbsp;           {"State/Province", "TerritoryDescription"}

&nbsp;       }),

&nbsp;       AddTerritoryID = Table.AddColumn(RenameColumns, "TerritoryID", each null),

&nbsp;       AddRegionID = Table.AddColumn(AddTerritoryID, "RegionID", each null),

&nbsp;       AddSource = Table.AddColumn(AddRegionID, "source\_prod", each "ACCESS"),

&nbsp;       SelectColumns = Table.SelectColumns(AddSource, {"EmployeeID", "LastName", "FirstName", "TerritoryID", "TerritoryDescription", "RegionID", "source\_prod"})

&nbsp;   in

&nbsp;       SelectColumns,



&nbsp;   // Version légèrement optimisée pour la combinaison :

&nbsp;   Combined = Table.Combine({SSMS\_Prepared, Excel\_Prepared}),

&nbsp;   

&nbsp;   // Ajout de l'ID séquentiel en une seule étape

&nbsp;   #"Added Index" = Table.AddIndexColumn(Combined, "id\_seqEmployee", 1, 1, Int64.Type),

&nbsp;   

&nbsp;   // Renommage final

&nbsp;   #"Renamed Columns" = Table.RenameColumns(#"Added Index", {

&nbsp;       {"EmployeeID", "id\_employee\_prod"},

&nbsp;       {"LastName", "Nom"},

&nbsp;       {"FirstName", "Prenom"},

&nbsp;       {"TerritoryID", "Territory"},

&nbsp;       {"TerritoryDescription", "TerritoryDescri"},

&nbsp;       {"RegionID", "Region"}

&nbsp;   }),

&nbsp;   #"Colonnes permutées" = Table.ReorderColumns(#"Renamed Columns",{"id\_seqEmployee", "id\_employee\_prod", "source\_prod", "Nom", "Prenom", "Territory", "TerritoryDescri", "Region"})

in

&nbsp;   #"Colonnes permutées"

