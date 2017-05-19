# Practices
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;
using System.Data;
using System.Data.SqlClient;
using System.Configuration;


namespace cascaded_dropdown.Controllers
{
    public class HomeController : Controller
    {

        SqlConnection conn = new SqlConnection(ConfigurationManager.ConnectionStrings["Pranav"].ConnectionString);
        public ActionResult Index()
        {
            getCountry();
            return View();
        }


        public void getCountry()
        {

            if (conn.State == ConnectionState.Closed)
            {
                conn.Open();
            }

            SqlCommand cmd = new SqlCommand("spGetCountry", conn);
            cmd.CommandType = CommandType.StoredProcedure;
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dt = new DataTable();
            da.Fill(dt);


            List<SelectListItem> list = new List<SelectListItem>();
            list.Add(new SelectListItem { Text = "--Select Country--", Value = "0" });

            foreach (DataRow drow in dt.Rows)
            {
                list.Add(new SelectListItem { Text = drow["country_name"].ToString(), Value = drow["country_id"].ToString() });
            }
            ViewData["CountryList"] = list;
        }

        public JsonResult getState(int id)
        {
            List<SelectListItem> list = new List<SelectListItem>();

            if (id < 1)
            {
                RedirectToAction("Index");
            }

            else
            {

                if (conn.State == ConnectionState.Closed)
                {
                    conn.Open();
                }

                SqlCommand cmd = new SqlCommand("spGetStateBycountryid", conn);
                cmd.CommandType = CommandType.StoredProcedure;
                cmd.Parameters.AddWithValue("@country_id", id);
                SqlDataAdapter da = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                da.Fill(dt);

                foreach (DataRow drow in dt.Rows)
                {
                    list.Add(new SelectListItem { Text = drow["state_name"].ToString(), Value = drow["state_id"].ToString() });
                }

            }

            return Json(new SelectList(list, "Value", "Text"));
        }


        public JsonResult getCity(int id)
        {
            List<SelectListItem> list = new List<SelectListItem>();

            if (id < 1)
            {
                RedirectToAction("Index");
            }

            else
            {

                if (conn.State == ConnectionState.Closed)
                {
                    conn.Open();
                }

                SqlCommand cmd = new SqlCommand("spGetCityBystateid", conn);
                cmd.CommandType = CommandType.StoredProcedure;
                cmd.Parameters.AddWithValue("@state_id", id);
                SqlDataAdapter da = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                da.Fill(dt);

                foreach (DataRow drow in dt.Rows)
                {
                    list.Add(new SelectListItem { Text = drow["city_name"].ToString(), Value = drow["city_id"].ToString() });
                }

            }

            return Json(new SelectList(list, "Value", "Text"));
        }

    }
}





        


    

