# 📊 Connecting to Global DHIS2 Using Tracker

Global DHIS2 reports are transformed and stored in a PostgreSQL container hosted on the Data Warehouse server (`10.100.11.42`). To access the DHIS2 Tracker interface in your browser, simply go to:

👉 [Open DHIS2 Tracker Interface](http://10.100.11.42:8095/)

If you want to visualize data from Global DHIS2 in Power BI, follow the steps below to connect directly to the PostgreSQL container.

---

## 🔌 Steps to Connect Power BI to Global DHIS2 PostgreSQL

### ✅ Step 1: Open Power BI and Select PostgreSQL

- Open **Power BI Desktop**
- Click on **Get Data**
- Click **More…**
- Under **Database**, choose **PostgreSQL database**

---

### ✅ Step 2: Enter Connection Details

If you’ve followed Step 1 correctly (which you probably did—unless you got distracted thinking the “More” button would lead you to a plate of nsima and beans 🍛), you'll now be prompted to enter connection details:

- **Server**: `10.100.11.42:5437`
- **Database**: `dhis2`

---

### ✅ Step 3: Enter Credentials

- **Username**: `postgres`
- **Password**: `xxxxxxxx`  
  _(Contact `thisisfumba` or `Kondwani Matiya` to get the actual password)_

---

### ✅ Step 4: Load the Required Tables

Once connected, select and load the following tables:

- `public.column_names_summary`
- `public.cross_site_kpi_monthly`
- `public.mental_health_quarterly`

---

### ✅ Step 5: Clean Up the Column Names Table

Start with the `public.column_names_summary` table. Go to **Advanced Editor** and update the query.

#### Default Query:
```powerquery
let
    Source = PostgreSQL.Database("10.160.22.17:5434", "dhis2"),
    public_column_names_summary = Source{[Schema="public",Item="column_names_summary"]}[Data]
in
    public_column_names_summary
```

#### Updated Query:
```powerquery
let
    Source = PostgreSQL.Database("10.160.22.17:5434", "dhis2"),
    public_column_names_summary = Source{[Schema="public",Item="column_names_summary"]}[Data],
    #"Removed Columns" = Table.RemoveColumns(public_column_names_summary, {"index"})
in
    #"Removed Columns"
```

This will make your table easier to work with and analysis-ready.

### ✅ Step 5: Clean Up the Column Names Table

Now work on the `public.mental_health_quarterly` table.

#### Default Query:
```powerquery
let  
    Source = PostgreSQL.Database("10.100.11.42:5437", "dhis2"),
    public_mental_health_quarterly = Source{[Schema="public",Item="mental_health_quarterly"]}[Data]  
in  
    public_mental_health_quarterly
```

#### Updated Query:
```powerquery
let  
    Source = PostgreSQL.Database("10.100.11.42:5437", "dhis2"),
    public_mental_health_quarterly = Source{[Schema="public",Item="mental_health_quarterly"]}[Data],
    rename_columns = Table.RenameColumns(public_mental_health_quarterly, Table.ToRows(#"public column_names_summary"), MissingField.Ignore)
in  
    rename_columns
```

This renames cryptic column headers using user-friendly names from the `column_names_summary` table.



