# LAB Tools

## CML

### Ubuntu

#### Bootstrap Configuration

    # this is a shell script which will be sourced at boot
    hostname Server-01
    # configurable user account
    USERNAME=cisco
    PASSWORD=cisco
    # no password for tc user by default
    TC_PASSWORD=
    #-------------------------------------------------------------------
    # Assign IP Address to Interfaces
    #-------------------------------------------------------------------
    #
    ip addr add [ip_adress/mask] brd [ip_address_broadcast] dev ens2
    #
    #
    #-------------------------------------------------------------------
    # Bring the Interfaces UP
    #-------------------------------------------------------------------
    #
    ip link set dev ens2 up
    #
    #
    #-------------------------------------------------------------------
    # Create the proxy.sh File to use proxy settings
    #-------------------------------------------------------------------
    #
    tee -a /etc/profile.d/proxy.sh <<EOF
    #-------------------------------------------------------------------
    # Set Proxy Config via profie.d
    #-------------------------------------------------------------------
    #
    export http_proxy="http://calo-rtp-proxy.cisco.com:3128/"
    export https_proxy="http://calo-rtp-proxy.cisco.com:3128/"
    export ftp_proxy="http://calo-rtp-proxy.cisco.com:3128/"
    export no_proxy="localhost,127.0.0.1,::1,*.cisco.com,.cisco.com,cisco.com"
    #
    # For curl
    #
    export HTTP_PROXY="http://calo-rtp-proxy.cisco.com:3128/"
    export HTTPS_PROXY="http://calo-rtp-proxy.cisco.com:3128/"
    export FTP_PROXY="http://calo-rtp-proxy.cisco.com:3128/"
    export NO_PROXY="localhost,127.0.0.1,::1,*.cisco.com,.cisco.com,cisco.com"
    EOF
    #
    #-------------------------------------------------------------------
    # Make proxy.sh executable
    #-------------------------------------------------------------------
    #
    sudo chmod +x  /etc/profile.d/proxy.sh
    #
    #-------------------------------------------------------------------
    # Create the proxy.conf File to use proxy in APT
    #-------------------------------------------------------------------
    #
    tee -a /etc/apt/apt.conf.d/proxy.conf <<EOF
    #-------------------------------------------------------------------
    # Set Proxy Config for APT
    #-------------------------------------------------------------------
    #
    #
    Acquire::http::proxy "http://calo-rtp-proxy.cisco.com:3128/";
    Acquire::https::proxy "http://calo-rtp-proxy.cisco.com:3128/";
    Acquire::ftp::proxy "http://calo-rtp-proxy.cisco.com:3128";
    EOF

#### Proxy for WGET

    vim ~/.wgetrc                           
 
     use_proxy = on
     http_proxy = http://calo-rtp-proxy.cisco.com:3128/ 
     https_proxy = http://calo-rtp-proxy.cisco.com:3128/ 
     ftp_proxy = http://calo-rtp-proxy.cisco.com:3128/ 


#### Tacacs+ w/ Docker

##### Step 1

    git clone https://github.com/llima3000/tacplus_image

    cd tacplus_image

    sudo docker build --build-arg http_proxy=http://calo-rtp-proxy.cisco.com:3128 --build-arg https_proxy=http://calo-rtp-proxy.cisco.com:3128 --no-cache -t tacplus .


##### Step 2

    sudo docker run -td --name tacplus -p 49:49 -v /home/cisco/tacplus_image/tac_plus.conf:/etc/tacacs+/tac_plus.conf tacplus

