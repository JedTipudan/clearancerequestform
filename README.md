right click Submit Request Events mouse submitrequestbtnMouseClicked
then copy this code below then paste it in there

if (acadyearcmbbx.getSelectedIndex()==0 || (!firstsemrequestrdobtn.isSelected() && !secondsemrequestrdobtn.isSelected())) {
    JOptionPane.showMessageDialog(this,"Please fill out all fields.","Information",JOptionPane.WARNING_MESSAGE);
    idnumbertxtbx.requestFocus();
    fullnametxtbx.requestFocus();
    coursetxtbx.requestFocus();
    acadyearcmbbx.requestFocus();
}else{
    String semester = "";
    if (firstsemrequestrdobtn.isSelected()) {
        semester = "First Sem";
    } else if (secondsemrequestrdobtn.isSelected()) {
        semester = "Second Sem";
    }
    try (Connection conn = DBConnection.getConnection()) {
        //CHECK IF STUDENT ALREADY REQUEST WITH THE SAME ACADEMIC YEAR AND SEMESTER
        String checkquery = "Select * FROM `clearancerequest` WHERE studentID=? AND acadyear=? AND semester=? LIMIT 1";
        PreparedStatement checkpst = conn.prepareStatement(checkquery);
        checkpst.setInt(1, studentID);
        checkpst.setString(2, acadyearcmbbx.getSelectedItem().toString());
        checkpst.setString(3, semester);
        ResultSet rs = checkpst.executeQuery();
        if (rs.next()) {
            //Student have already requested with the same acadyear and semester
            JOptionPane.showMessageDialog(this,"You cannot proceed-a request for the same academic year and semester already exists.",
                    "Information",JOptionPane.WARNING_MESSAGE);
        } else {
            //INSERT REQUEST
            String insertQuery = "INSERT INTO `clearancerequest`(`studentID`, `acadyear`, `semester`, `dateRequested`) "
                    + "VALUES (?,?,?,NOW())";
            PreparedStatement psrequest = conn.prepareStatement(insertQuery, Statement.RETURN_GENERATED_KEYS);
            psrequest.setInt(1, studentID);
            psrequest.setString(2, acadyearcmbbx.getSelectedItem().toString());
            psrequest.setString(3, semester);
            psrequest.executeUpdate();

            ResultSet getrs = psrequest.getGeneratedKeys();
            int clearancerequestID = 0;
            if (getrs.next()) {
                clearancerequestID = getrs.getInt(1);
            }
            psrequest.close();

            //GET CLEARING OFFICES
            String getquery = "Select * FROM `clearingoffices`";
            PreparedStatement pstClearOffice = conn.prepareStatement(getquery);
            ResultSet clearingofficers = pstClearOffice.executeQuery();

            //INSERT CLEARING OFFICES ALONG WITH CLEARANCE REQUESTID
            String sqlclearancestatus = "INSERT INTO clearancestatus (clearancerequestID, clearingofficesID, status) VALUES (?,?,?)";
            PreparedStatement psstatus = conn.prepareStatement(sqlclearancestatus);
            while(clearingofficers.next()){
                int clearingofficeID=clearingofficers.getInt("clearingofficesID");

                psstatus.setInt(1, clearancerequestID);
                psstatus.setInt(2, clearingofficeID);
                psstatus.setString(3, "Pending");
                psstatus.executeUpdate();
            }
            clearfields();
            GetClearanceRequest();
            JOptionPane.showMessageDialog(this,"Clearance Request has been submitted to offices.","Information",
                    JOptionPane.INFORMATION_MESSAGE);
            

        }
    }catch(SQLException ae){
        JOptionPane.showMessageDialog(this, "Error!", "Information", JOptionPane.WARNING_MESSAGE);

    }
}
