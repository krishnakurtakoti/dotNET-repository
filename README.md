# dotNET-repository
Krishna's DotNet Collection

			SIMPLE WEB API .NET IN VISUAL STUDIO

	Requirements/Implementaion: Visual Studio 2017 and MSSQL Server 2008 R2
 NOTE:   CommonApplicationFramework can be downloaded from the same repository.It shoul be added to the references.  
    		In 
		
		Packages > CAF 1.0.0.1 > CommonApplicationFramework.dll 


STEPS:
1.Create project ASP.NET Web Application with Web API as option.
	
	    testDiscountProject2(main project)
2.Create 3 more projects of ASP.Net with Empty as option and name them as
      
      testDiscountProject2.Business(new project -ASP.NET Web Application > Empty)
      testDiscountProject2.Models(new project -ASP.NET Web Application > Empty)
      testDiscountProject2.Repositori(new project -ASP.NET Web Application > Empty)
3.It's time to add references to  
				  
				  testDiscountProject2(main project),
                                  testDiscountProject2.Business,
                                  testDiscountProject2.Repositori.
   
   
   Add CommonApplicationFramework.dll in all the References.Also add as follows:
   
   
    testDiscountProject2(main project)
	    References-CommonApplicationFramework
				  -testDiscountProject2.Business
				  -testDiscountProject2.Models
          
      testDiscountProject2.Business(new project -ASP.NET Web Application > Empty)
		References-CommonApplicationFramework
				  -testDiscountProject2.Models
				  -testDiscountProject2.Repositori
          
          
        testDiscountProject2.Repositori(new project -ASP.NET Web Application > Empty)
		References-CommonApplicationFramework
				  -testDiscountProject2.Models  
4.In  testDiscountProject2.Business,make 2 new folders 
			
			Business and IBusiness.
			Add DiscountBusiness(class) and IDiscountBusiness(interface).
			
			
			****************************************************************************************
			DiscountBusiness.cs
			
			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Web;
			using testDiscountProject2.Business.IBusiness;
			using testDiscountProject2.Models;
			using testDiscountProject2.Repositori.IRepository;
			using testDiscountProject2.Repositori.Repository;

			namespace testDiscountProject2.Business.Business
			{
			    public class DiscountBusiness : IDiscountBusiness
			    {
				private readonly IDiscountRepository _IRepository;
				public DiscountBusiness()
				{
				    _IRepository = new DiscountRepository();
				}

				public List<Discount> GetList()
				{
				    return _IRepository.GetListInRepository();
				  // throw new NotImplementedException();
				}
			    }
			}
			
			
			
			***********************************************************************************
			IDiscountBusiness.cs


			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Text;
			using System.Threading.Tasks;
			using testDiscountProject2.Models;

			namespace testDiscountProject2.Business.IBusiness
			{
			    public interface IDiscountBusiness
			    {
				List<Discount> GetList();
			    }
			}
			
			
5.In  testDiscountProject2.Repositori,make 2 new folders 

			Repository and IRepository.
			Add DiscountRepository(class) and IDiscountRepository(interface).
			
			*********************************************************************************************
			DiscountRepository.cs
			
			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Web;
			using testDiscountProject2.Models;
			using testDiscountProject2.Repositori.IRepository;
			using CommonApplicationFramework.DataHandling;
			using System.Configuration;
			using System.Data;
			using System.Data.SqlClient;
			namespace testDiscountProject2.Repositori.Repository
			{
			    public class DiscountRepository : IDiscountRepository
			    {
				private DBManager dbManager;
				public string ConnectionString { get; set; }
				public List<Discount> GetListInRepository()
				{
				    Discount discountDetail = new Discount();
				    List<Discount> discountList = new List<Discount>();

				    try
				    {
					/* using (dbManager = new DBManager())
					{
					    return discountList;
					}

					 */

					ConnectionString = ConfigurationManager.ConnectionStrings["NorthwindDbConnection"].ConnectionString;
					SqlConnection sqlConnection = new SqlConnection(ConnectionString);
					sqlConnection.Open();
					 SqlCommand sqlCommand1 = new SqlCommand("SELECT ID, Name, IsActive, IsDeleted "
							    + " FROM dbo.Discount",sqlConnection);

					SqlDataReader dr = sqlCommand1.ExecuteReader();
					{
					   while (dr.Read())
					    {
						discountDetail = new Discount();
						discountDetail.ID = ConvertData.ToString(dr["ID"]);
						discountDetail.Name = Convert.ToString(dr["Name"]);
						discountDetail.IsActive = Convert.ToInt32(dr["IsActive"]);
						discountDetail.IsDeleted = Convert.ToInt32(dr["IsDeleted"]);
						discountList.Add(discountDetail);
					    }

					    sqlConnection.Close();
					    //return discountList;
					}

				       // sqlConnection.Close();
						   return discountList;
				    }
				    catch(Exception)
				    {
					throw new NotImplementedException();
				    }
				}
			    }
			}
			
			
			*********************************************************************************************		
			IDiscountRepository.cs
			
			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Text;
			using System.Threading.Tasks;
			using testDiscountProject2.Models;

			namespace testDiscountProject2.Repositori.IRepository
			{
			    public interface IDiscountRepository
			    {
				List<Discount> GetListInRepository();
			    }
			}



			
			
6.In  testDiscountProject2.Models,

			add Discount.cs as class.
			
			***********************************************************************************
			Discount.cs
		
			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Web;

			namespace testDiscountProject2.Models
			{
			    public class Discount
			    {
				public string ID { get; set; }
				public string Name { get; set; }
				public int IsActive { get; set; }
				public int IsDeleted { get; set; }
			    }
			}
			
7.In  testDiscountProject2,
			
			add new controller named DiscountController.cs
			
			
					******************************************************************************************************	
			DiscountController.cs
			
		
			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Net;
			using System.Net.Http;
			using System.Web.Http;
			using testDiscountProject2.Business.Business;
			using testDiscountProject2.Business.IBusiness;
			using testDiscountProject2.Models;

			namespace testDiscountProject2.Controllers
			{
			    public class DiscountsController : ApiController
			    {
				private readonly IDiscountBusiness _IDiscountBusiness;


				public DiscountsController()
				{
				    _IDiscountBusiness = new DiscountBusiness();
				}

			     /*   public DiscountsController(IDiscountBusiness iDiscountBusiness)
				{
				    _IDiscountBusiness = iDiscountBusiness;
				}
			     */


				[HttpGet,ActionName("GetDiscountList")]
				public IHttpActionResult Get()
				{
				    try
				    {
					List<Discount> discountList = _IDiscountBusiness.GetList();
					if(discountList != null && discountList.Count > 0)
					{
					    return Content(HttpStatusCode.OK, discountList);
					}
					return Content(HttpStatusCode.NotFound, "NOT FOUND");
				    }
				    catch(Exception ex)
				    {
					throw new NotImplementedException();
				    }
				}

			    }
			}


8.In  WebApiConfig.cs in the   testDiscountProject2(main project),add the following:

	config.Routes.MapHttpRoute(
             name: "DefaultApi2",
             routeTemplate: "api/{controller}/{action}/{code}/{id}",
             defaults: new { id = RouteParameter.Optional, code = RouteParameter.Optional }
            );
	    
Full File
			WebApiConfig.cs

			using System;
			using System.Collections.Generic;
			using System.Linq;
			using System.Web.Http;

			namespace testDiscountProject2
			{
			    public static class WebApiConfig
			    {
				public static void Register(HttpConfiguration config)
				{
				    // Web API configuration and services

				    // Web API routes
				    config.MapHttpAttributeRoutes();

				    config.Routes.MapHttpRoute(
				     name: "DefaultApi2",
				     routeTemplate: "api/{controller}/{action}/{code}/{id}",
				     defaults: new { id = RouteParameter.Optional, code = RouteParameter.Optional }
				    );



				    config.Routes.MapHttpRoute(
					name: "DefaultApi",
					routeTemplate: "api/{controller}/{id}",
					defaults: new { id = RouteParameter.Optional }
				    );
				}
			    }
			}
	    
	    
	    
	    
 9.In Web.config, add at the top of the file with your 
 
 	MS SQLServer Name,Database Name,Login Id and password.:
 
 	<connectionStrings>
   	<add name="NorthwindDbConnection" providerName="System.Data.SqlClient"

         connectionString="Data Source=DESKTOP-H8LBM5I;initial catalog=testDB; User Id=sa; password=***_***"/>
  	</connectionStrings>
	
	
	
  10.Save all and check for no errors.
  11.Open postman and write the GET request:GET method

		http://localhost:62161/api/Discounts/GetDiscountList

2.No Params

3.In Headers,
			
			URL             			 http://localhost:62161/
			Content-Type				 application/json
			
4.Click Send.
********************************************************************************************************************
				WORKFLOW:
				
1.Apply breakpoint in DiscountController at:
				

			 [HttpGet,ActionName("GetDiscountList")]
        public IHttpActionResult Get()
        {
	*>>>>       try
            {
            List<Discount> discountList = _IDiscountBusiness.GetList();
                if(discountList != null && discountList.Count > 0)
                {
                    return Content(HttpStatusCode.OK, discountList);
                }
                return Content(HttpStatusCode.NotFound, "NOT FOUND");
            }
            catch(Exception ex)
            {
                throw new NotImplementedException();
            }
        }

		
*>>>> BreakPoint

Hit F10 to go to next step.	
When it reaches 
		
		List<Discount> discountList = _IDiscountBusiness.GetList(); ,hit F11.	
Hit F11 to go into the method.

You will go to DiscountBusiness.cs
 
 	public List<Discount> GetList()
        {
            return _IRepository.GetListInRepository();
          // throw new NotImplementedException();
        }
	
Again hit F11 at 	
	
	return _IRepository.GetListInRepository();
Continue ......	



          
          
          
          
      
