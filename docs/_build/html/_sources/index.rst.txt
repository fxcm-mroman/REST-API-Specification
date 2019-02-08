.. REST API Specification documentation master file, created by
   sphinx-quickstart on Thu Feb  7 23:25:32 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

REST API Specification
======================

FXCM Socket REST API User Guide
-------------------------------

.. tabularcolumns:: |p{1cm}|p{2cm}|p{3cm}|
	
.. csv-table:: Revision History
   :file: _files/revhist.csv
   :header-rows: 1
   :class: longtable
   :widths: 1 1 1
   :align: center
 	 
**Contents**

	1. Overview

	
	2. Getting Started	

		2.1. Prerequisites	
		
		2.2. Logon and Authentication
			
		2.3. Message Flow	
		
	3. Authentication Messages	

		3.1. Open Socket and get socket_id by passing access_token	
		
	4. Market Data	

		4.1. Request a list of all available symbols	
		
		4.2. Subscribe to Market Data stream	
		
		4.3. Unsubscribe from Market Data stream	
		
		4.4. Price updates	
		
	5. Trading Tables		

		5.1. Subscribe to trading tables	
		
		5.2. Unsubscribe from trading tables
			
		5.3. Request a snapshot of trading tables
			
		5.4. Trading table responses	
	
			5.4.1. Offers table	
			
			5.4.2. Open Positions table	
			
			5.4.3. Closed Positions table	
			
			5.4.4. Orders table	
			
			5.4.5. Summary table
				
			5.4.6. Accounts table
				
	6. Trading Orders
	
		6.1. /trading/open_trade	
		
		6.2. /trading/close_trade	
		
		6.3. /trading/change_order	
		
		6.4. /trading/delete_order	
		
		6.5. /trading/create_entry_order
			
		6.6. /trading/simple_oco
			
		6.7. /trading/add_to_oco
		
		6.8. /trading/remove_from_oco
			
		6.9. /trading/edit_oco	
		
		6.10. /trading/change_trade_stop_limit	
		
		6.11. /trading/change_order_stop_limit	
		
		6.12. /trading/close_all_for_symbol	
		
	7. Historical Data	
	

.. toctree::
   :maxdepth: 6
   :caption: Contents:

   socketrestapispecs


Indices and tables
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
