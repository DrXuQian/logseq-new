title:: How to configure wsl proxy?

- System proxy:
	- export https_proxy=https://child-prc.intel.com:913
	  export http_proxy=https://child-prc.intel.com:913
- Apt proxy:
	- Acquire::http::Proxy "http://child-prc.intel.com:913";
	  Acquire::https::Proxy "https://child-prc.intel.com:913";