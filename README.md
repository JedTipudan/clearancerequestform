right click Submit Request Events mouse submitrequestbtnMouseClicked
then copy the code on clearancerequestform.java you see on there


after you paste then you get error then your database have some issue if it you can manually fix it or use AI its up to you now good luck and have fun

or if you don`t get it then click this link for faster copy the code and paste it to your output
https://pastecode.dev/s/yyg39cvs

for filter

https://codeshare.io/GAmjlx


follow this steps for the jcomboBox activity - - --- - - -          ___________________________________________________
___
____
_____
___
Paste this code on the cleareance request sql on database
__
__

SELECT *, concat(t1.acadyear,'-',t1.semester) as aysem FROM clearancerequest as t1 LEFT JOIN clearancestatus as t2 ON t1.clearancerequestID=t2.clearancerequestID JOIN clearingoffices as t3 ON t3.clearingofficesID=t2.clearingofficesID where t1.studentID=2 GROUP BY aysem;
<img width="1920" height="1200" alt="{950EEFF6-1B60-468F-B8A6-402F4425B71E}" src="https://github.com/user-attachments/assets/ef339075-154b-4d94-bbd4-5de41c174cbc" />

after procced to the next step
paste the code or insert it see where i insert in on the photo
<img width="1920" height="1200" alt="{0A4E3AC5-9C47-4699-BCC5-6EEFA512941E}" src="https://github.com/user-attachments/assets/6bedcd70-05b4-4dcf-9149-cae1be9487cf" />

paste this code

    GetAYSem();  
    
    }
    private void GetAYSem(){
        try (Connection conn = DBConnection.getConnection()) {
        String query = "SELECT concat(t1.acadyear,'-',t1.semester) as aysem FROM clearancerequest as "
        + "t1 LEFT JOIN clearancestatus as t2 ON t1.clearancerequestID=t2.clearancerequestID "
        + "JOIN clearingoffices as t3 ON t3.clearingofficesID=t2.clearingofficesID where t1.studentID=? GROUP BY aysem ORDER BY aysem DESC;";

                PreparedStatement checkpst = conn.prepareStatement(query);
                checkpst.setInt(1, studentID);
                ResultSet rs = checkpst.executeQuery();

                DefaultComboBoxModel<String> model = new DefaultComboBoxModel<>();
                JComboBox<String> comboBox = new JComboBox<>(model);

                // Loop through the result set and add items to the comboBox model
                while (rs.next()) {
                    String aysem = rs.getString("aysem");
                    model.addElement(aysem);
                }

                // If you want to set the model to a specific comboBox (acadyearcmbbx2)
                acadyearcmbbx2.setModel(model);

      }catch(SQLException ae){
          
      }
    }



