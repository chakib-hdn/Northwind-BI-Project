#### Dim\_Client

let

    // Partie SSMS

    Client\_SSMS = let

        Source = Customers\_ssms,

        #"Selected Columns" = Table.SelectColumns(Source, {"CustomerID", "CompanyName", "City"}),

        #"Renamed Columns" = Table.RenameColumns(#"Selected Columns", {

            {"CustomerID", "id\_client\_prod"},

            {"CompanyName", "CompanyName"}

        }),

        #"Added Source" = Table.AddColumn(#"Renamed Columns", "source\_prod", each "SSMS")

    in

        #"Added Source",



    // Partie Excel (ACCESS)

    Client\_ACCESS = let

        Source = Customers\_excel,

        #"Selected Columns" = Table.SelectColumns(Source, {"ID", "Company", "City"}),

        #"Renamed Columns" = Table.RenameColumns(#"Selected Columns", {

            {"ID", "id\_client\_prod"},

            {"Company", "CompanyName"}

        }),

        #"Added Source" = Table.AddColumn(#"Renamed Columns", "source\_prod", each "ACCESS")

    in

        #"Added Source",



    // Union des deux sources

    Unioned = Table.Combine({Client\_SSMS, Client\_ACCESS}),

 

    // Ajout de l'ID séquentiel

    #"Added Index" = Table.AddIndexColumn(Unioned, "id\_seqClient", 1, 1, Int64.Type),

    #"Colonnes permutées" = Table.ReorderColumns(#"Added Index",{"id\_seqClient", "id\_client\_prod", "source\_prod", "CompanyName", "City"})

in

    #"Colonnes permutées"

