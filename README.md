# lpic2-studynotes
LPIC 2 Linux Engineering Exam Important Study notes and example
*******************************
Topic 207: Domain Name Server *
*******************************
Basic DNS Server Configuration

DNS client configuration is simple: Just specify the DNS server in the local system’s /etc/resolv.conf file:

[root@localhost ~]# cat /etc/resolv.conf
nameserver 192.168.1.1

Essential Terms

Host—Typically a host is a computer attached to a network.

Domain name—Hosts on the Internet address each other by using IP address numbers.

Top-level domain—The top level of the DNS structure is simply referred to as “dot” and symbolized by the “.” character. The domains directly beneath “.” are the top-level domains. The original top-level domains were .com, .org, .net, .int, .edu, .gov, and .mil. Many others have been added in recent years.

FQDN—A Fully Qualified Domain Name is the domain name of a host starting from the top of the DNS structure. For example, the name www.onecoursesource.com. would be an FQDN. Notice the “.” character at the end of the FQDN

Subdomain—A subdomain is any domain that is a component of a larger domain.If your company’s domain is onecoursesource.com., these three subdomains would be called sales.onecoursesource.com., eng.onecoursesource.com., and support.onecoursesource.com.

Name server—A name server is a system that responds to DNS client requests. Name servers provide the translation from IP address to domain names (and sometimes provide the opposite: domain name to IP address translation).

Authoritative name server—An authoritative name server is one that returns results based on information stored locally on the system (the original master records).

Zone file—The name of the file used to store IP address-to-domain name translation information (also known as the DNS records). This file also contains information used to define the domain itself.

Record—Within the zone file a record is an entry that defines a single chunk of information for the zone, such as the data that would translate an IP address to domain name.

Caching name server—A caching name server is one that returns results based on information obtained from another name server, such as an authoritative name server.

TTL—The data stored in a caching name server is typically not stored permanently. The name server that provides the data also provides the caching name server with a TTL, a time to live. The caching name server stores the information in memory until this TTL period ends

DNS forwarder—A DNS server designed to take DNS queries from an internal network and send the queries to an external DNS server.

Forward lookup—The process of translating an IP address into a domain name. Most DNS servers provide this functionality.

Reverse lookup—The process of translating a domain name into an IP address.

BIND—Berkeley Internet Name Domain; the DNS software most widely used on the Internet.

dnsmasq—A server that can be used as a DNS forwarder. It can also be used as a DHCP (Dynamic Host Configuration Protocol) server. It is designed for smaller networks and is known for being easy to configure as well as for having a low overhead regarding system resources.

djbdns—Released in 2001, this DNS server was created by Daniel J. Bernstein as the result of his frustrations due to multiple security holes found in BIND.

PowerDNS—Initially PowerDNS was a closed source software project when it was first released in 1999. It was originally designed to provide a feature called load balancing, where different IP addresses were provided by the DNS server to spread the load of URL requests between multiple web servers. It was released as open source in 2003.

Basic Bind Configuration

The /etc/named.conf file

The primary configuration file for the BIND server is the /etc/named.conf file. This file is read by the BIND server (named) when it is started.

options {
        listen-on port 53 { 127.0.0.1; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { localhost; };

         recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;
        dnssec-lookaside auto;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.iscdlv.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

