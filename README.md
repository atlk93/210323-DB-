# 210323-DB-
C# DB

```
Form1
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Data.SqlClient;
using System.Drawing;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

// 1. file select
// 2. open and read
// 3. Grid header gen
// 4. Record append
// 5. Input to Cell

namespace DBManager
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void mnuMigration_Click(object sender, EventArgs e)
        {
            DialogResult ret = openFileDialog1.ShowDialog();
            if (ret != DialogResult.OK) return;
            string nFile = openFileDialog1.FileName;    // full name

            StreamReader sr = new StreamReader(nFile);

            // ==================================================
            //      Header 처리 프로세스
            // ==================================================
            string buf = sr.ReadLine();     // 1 Line Read
            if (buf == null) return;                
            string[] sArr = buf.Split(',');
            for(int i=0;i<sArr.Length;i++)
            {
                dataGrid.Columns.Add(sArr[i], sArr[i]);
            }

            // ==================================================
            //      Row 데이터 처리 프로세스
            // ==================================================
            while(true)
            {
                buf = sr.ReadLine();
                if (buf == null) break;
                sArr = buf.Split(',');
                //dataGrid.Rows.Add(sArr);
                int rIdx = dataGrid.Rows.Add(); // 1 Line 생성
                for (int i = 0; i < sArr.Length; i++)
                {
                    dataGrid.Rows[rIdx].Cells[i].Value = sArr[i];
                }
            }
        }
        SqlConnection sqlCon = new SqlConnection();
        SqlCommand sqlCmd = new SqlCommand();
        string sConn = @"Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=;Integrated Security=True;Connect Timeout=30";

        private void mnuDBOpen_Click(object sender, EventArgs e)
        {
            try
            {
                DialogResult ret = openFileDialog1.ShowDialog();    // db file
                if (ret != DialogResult.OK) return;
                string nFile = openFileDialog1.FileName;    // full name
                string[] ss = sConn.Split(';');

                sqlCmd.Connection = sqlCon;
                sqlCon.ConnectionString = $"{ss[0]};{ss[1]}{nFile};{ss[2]};{ss[3]}";
                sqlCon.Open();
                sbPanel1.Text = openFileDialog1.SafeFileName;
                sbPanel2.Text = "Database opened success.";
                sbPanel2.BackColor = Color.Green;
            }
            catch(SqlException e1)
            {
                MessageBox.Show(e1.Message);
                sbPanel2.Text = "Database Cannot open.";
                sbPanel2.BackColor = Color.Red;
            }
        }

        int RunSql(string sql)
        {
            try
            {
                sqlCmd.CommandText = sql;
                sqlCmd.ExecuteNonQuery();   // select 문 제외 -- no return value
                                            // update, insert, delete, create, alt
             // sqlCmd.ExecuteReader();
            }
            catch (SqlException e1)
            {
                MessageBox.Show(e1.Message);
            }
            catch(InvalidOperationException e2)
            {
                MessageBox.Show(e2.Message);
            }
            return 0;
        }

        private void mnuExecSql_Click(object sender, EventArgs e)
        {
            RunSql(tbSql.Text);
        }

        private void mnuSelSql_Click(object sender, EventArgs e)
        {
            RunSql(tbSql.SelectedText);
            //string sql = tbSql.SelectedText;
            //sqlCmd.CommandText = sql;
            //sqlCmd.ExecuteNonQuery();
        }
    }
}

```
