right click Submit Request Events mouse submitrequestbtnMouseClicked
then copy the code on clearancerequestform.java you see on there


after you paste then you get error then your database have some issue if it you can manually fix it or use AI its up to you now good luck and have fun

or if you don`t get it then click this link for faster copy the code and paste it to your output
https://pastecode.dev/s/yyg39cvs

acadyearcmbbx2.addActionListener(e -> filterClearanceRequest());
under initComponents

then paste this on the first sem second sem on the top then paste this
filterClearanceRequest();

then put this under the clearfields
private void filterClearanceRequest() {
    String selectedYear = acadyearcmbbx2.getSelectedItem().toString();
    String selectedSem = "";

    if (firstsemrequestrdobtn1.isSelected()) {
        selectedSem = "First Sem";
    } else if (secondsemrequestrdobtn1.isSelected()) {
        selectedSem = "Second Sem";
    }

    // If no filter is selected, clear the table and exit
    if ("Select AY".equals(selectedYear) && selectedSem.isEmpty()) {
        javax.swing.table.DefaultTableModel emptyModel = new javax.swing.table.DefaultTableModel();
        emptyModel.addColumn("Clearing Offices");
        emptyModel.addColumn("Academic Year and Sem");
        emptyModel.addColumn("Status");
        emptyModel.addColumn("Remarks");
        emptyModel.addColumn("Date Requested");
        jTable1.setModel(emptyModel);
        return;
    }

    try (Connection conn = DBConnection.getConnection()) {
        String query = "SELECT *, CONCAT(t1.acadyear,'-',t1.semester) AS aysem " +
                "FROM clearancerequest AS t1 " +
                "LEFT JOIN clearancestatus AS t2 ON t1.clearancerequestID = t2.clearancerequestID " +
                "JOIN clearingoffices AS t3 ON t3.clearingofficesID = t2.clearingofficesID " +
                "WHERE t1.studentID = ? ";

        // Apply filters
        if (!"Select AY".equals(selectedYear)) {
            query += " AND t1.acadyear = ? ";
        }
        if (!selectedSem.isEmpty()) {
            query += " AND t1.semester = ? ";
        }

        PreparedStatement pst = conn.prepareStatement(query);

        int paramIndex = 1;
        pst.setInt(paramIndex++, studentID);
        if (!"Select AY".equals(selectedYear)) {
            pst.setString(paramIndex++, selectedYear);
        }
        if (!selectedSem.isEmpty()) {
            pst.setString(paramIndex++, selectedSem);
        }

        ResultSet rs = pst.executeQuery();

        javax.swing.table.DefaultTableModel col = new javax.swing.table.DefaultTableModel();
        col.addColumn("Clearing Offices");
        col.addColumn("Academic Year and Sem");
        col.addColumn("Status");
        col.addColumn("Remarks");
        col.addColumn("Date Requested");

        while (rs.next()) {
            col.addRow(new Object[]{
                    rs.getString("clearingoffice"),
                    rs.getString("aysem"),
                    rs.getString("status"),
                    rs.getString("remarks"),
                    rs.getString("dateRequested"),
            });
        }

        jTable1.setModel(col);
        rs.close();
        pst.close();
    } catch (SQLException ae) {
        JOptionPane.showMessageDialog(this, "Error while filtering data!", "Information", JOptionPane.WARNING_MESSAGE);
    }
}


