✅ DETAILED EXPLANATION WITH CODE + WHAT THAT CODE IS (parameter, condition, query, renderer, etc.)
🟦 1. Database Connection
try (Connection conn = DBConnection.getConnection()) {


→ TYPE: database connection initializer (resource block)
→ PURPOSE: Opens a connection to the MySQL database.

🟦 2. Getting Filters From UI
(A) Get selected academic year
String selectedAY = (acadyearcmbbx2.getSelectedItem() != null) ? 
                    acadyearcmbbx2.getSelectedItem().toString() : "";


→ TYPE: condition + variable assignment
→ PURPOSE:

If the combo box has a value → use it

If not → use empty string
This ensures the filter does not break.

(B) Get selected semester
String semester = "";
if (firstsemrequestrdobtn1.isSelected()) semester = "First Sem";
else if (secondsemrequestrdobtn1.isSelected()) semester = "Second Sem";


→ TYPE: conditional statement
→ PURPOSE:

Checks which radio button is selected

Assigns the correct semester text to the variable

🟦 3. Building the SQL Query
StringBuilder query = new StringBuilder(
    "SELECT t3.clearingoffice, CONCAT(t1.acadyear,'-',t1.semester) AS aysem, " +
    "t2.status, t2.remarks, t1.dateRequested " +
    "FROM clearancerequest AS t1 " +
    "LEFT JOIN clearancestatus AS t2 ON t1.clearancerequestID = t2.clearancerequestID " +
    "JOIN clearingoffices AS t3 ON t3.clearingofficesID = t2.clearingofficesID " +
    "WHERE t1.studentID = ?"
);


→ TYPE: SQL SELECT query (base query)
→ PURPOSE:
Retrieves:

Clearing office name

AY + Semester (combined)

Status

Remarks

Date request

→ Contains a parameter placeholder:
? for studentID.

🟦 4. Adding Filters (Dynamic SQL Conditions)
Filter for Academic Year
if (!"All".equals(selectedAY)) query.append(" AND t1.acadyear = ?");


→ TYPE: condition + SQL filter + parameter placeholder
→ PURPOSE:
If AY is not "All", add a filter to the SQL query.

Filter for Semester
if (!semester.isEmpty()) query.append(" AND t1.semester = ?");


→ TYPE: condition + SQL filter + parameter placeholder
→ PURPOSE:
If semester is selected, add another condition.

Sorting
query.append(" ORDER BY t1.dateRequested DESC;");


→ TYPE: SQL order clause
→ PURPOSE:
Sort newest clearance requests first.

🟦 5. Prepare SQL Statement and Set Parameters
PreparedStatement pst = conn.prepareStatement(query.toString());
int paramIndex = 1;


→ TYPE: PreparedStatement creation
→ PURPOSE:
Allows safe insertion of parameters (?).

Parameter 1 → studentID
pst.setInt(paramIndex++, studentID);


→ TYPE: parameter binding
→ PURPOSE:
Replaces the first ? in the SQL query with the student ID.

Optional parameter → selectedAY
if (!"All".equals(selectedAY)) pst.setString(paramIndex++, selectedAY);


→ TYPE: conditional parameter binding
→ PURPOSE:
Only sets this parameter if the filter was applied.

Optional parameter → semester
if (!semester.isEmpty()) pst.setString(paramIndex++, semester);


→ TYPE: conditional parameter binding
→ PURPOSE:
Adds semester parameter only if a semester is selected.

🟦 6. Execute the Query
ResultSet rs = pst.executeQuery();


→ TYPE: SQL execution
→ PURPOSE:
Runs the query and returns matching records.

🟦 7. Creating the Table Model
DefaultTableModel model = new DefaultTableModel();
model.addColumn("Clearing Offices");
model.addColumn("Academic Year and Sem");
model.addColumn("Status");
model.addColumn("Remarks");
model.addColumn("Date Requested");


→ TYPE: table model setup
→ PURPOSE:
Defines column headers for the JTable.

🟦 8. Filling the Table Model with Data
boolean hasData = false;

while (rs.next()) {
    hasData = true;
    model.addRow(new Object[]{
        rs.getString("clearingoffice"),
        rs.getString("aysem"),
        rs.getString("status"),
        rs.getString("remarks"),
        rs.getString("dateRequested")
    });
}


→ TYPE: loop + data extraction
→ PURPOSE:

Reads each row from the database

Adds each row to the JTable model

If no data found
if (!hasData) {
    model.addRow(new Object[]{"No data found", "", "", "", ""});
}


→ TYPE: condition
→ PURPOSE:
Shows a "No data found" placeholder row.

🟦 9. Apply Model to JTable
clearancestatusIDlbl.setModel(model);


→ TYPE: table model assignment
→ PURPOSE:
Displays the loaded data in the UI.

🟦 10. Apply Row Coloring (Renderer)
clearancestatusIDlbl.setDefaultRenderer(Object.class, new DefaultTableCellRenderer() {


→ TYPE: custom renderer
→ PURPOSE:
Allows changing row colors based on value.

Renderer logic
public Component getTableCellRendererComponent(JTable table, Object value,
        boolean isSelected, boolean hasFocus, int row, int column) {


→ TYPE: method override
→ PURPOSE:
Defines how each cell looks.

Check status column (index 2)
String status = table.getValueAt(row, 2) != null ? 
                table.getValueAt(row, 2).toString() : "";


→ TYPE: condition + value extraction
→ PURPOSE:
Gets the status of the current row.

Apply row colors
if (!isSelected) {
    if (status.equalsIgnoreCase("Cleared")) {
        c.setBackground(Color.GREEN);
        c.setForeground(Color.BLACK);
    } else {
        c.setBackground(Color.WHITE);
        c.setForeground(Color.BLACK);
    }
}


→ TYPE: conditional formatting (row coloring)
→ PURPOSE:

Cleared → GREEN

Not cleared → WHITE

This makes cleared records easy to identify.

🟦 11. Close Resources
rs.close();
pst.close();


→ TYPE: resource cleanup
→ PURPOSE:
Prevents memory leaks and improves performance.

🟦 12. Error Handling
} catch (SQLException ex) {
    JOptionPane.showMessageDialog(this, "Error!", "Information", JOptionPane.WARNING_MESSAGE);
}


→ TYPE: exception handling
→ PURPOSE:
Displays a warning if something goes wrong.
