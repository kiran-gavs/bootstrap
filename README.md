0(root@chefws1 /home/engineering/bootstrap)$ cat bootstrap_program.pl
#!/usr/bin/perl
#

use strict;


sub main (){

use Switch;

print "\nMulti node bootstrap programi\n

1: Bootstrap Linux servers \n
2: Heartbeat configuration for Linux servers \n
3: Bootstrap Windows servers \n
4: Heartbeat configuration for Windows servers \n
5: Exit \n\n";

print "Enter the option to perform : ";
my $options = <STDIN>;
chomp $options;

switch($options){

            case 1 { bootstrap_linux();       }

            case 2 { linux_chef_heartbeat(); main();       }

            case 3 { bootstrap_windows(); main();        }

            case 4 { windows_chef_heartbeat(); main();        }

            else

                {
                print "\nDo you really want to exit!! :( :  - Yes/No : ";
                my $inp_ops = <STDIN>;
                if($inp_ops =~ /yes/i){
                print "\n\n Thanks for using the program!!.. See you Soon!!!...\n";
                exit;
                    }
                    else { main();}
                }
            }
        }

############################################################################################

sub bootstrap_linux(){

print "\nList of Linux servers\n";

system("cat Lservers_list.txt");

print "\n Is this the list of servers You want to bootstrap - Yes/No : ";
my $input1 = <STDIN>;

if($input1 =~ /yes/i){

print "\n Enter key for the servers : ";
my $key = <STDIN>;
chomp $key;

print "\n Enter Environment name : ";
my $env = <STDIN>;
chomp $env;

my $count = 1;
system ("mkdir -p ./bootstrap_logs/$env");
my $logpath = "./bootstrap_logs/$env";

open my $file_in, '<', 'Lservers_list.txt' or die $!;
while(<$file_in>){
        my $count=$count++;
        my @columns = split /\ /, $_;

        my $data1 = "$columns[0]";
        chomp $data1;
        my $data2 = "$columns[1]";
        chomp $data2;

print "\n\nBootstrap for server $count : $data1 \n";
my $cmd1=system ("knife bootstrap $data1 --ssh-user ec2-user -i ./keys/$key -E $env -N $data2  --sudo 2>&1 | tee $logpath/$data1.logerr & ");
}
close $file_in;
}
}

###################################################################

sub linux_chef_heartbeat(){

print "\nList of Linux servers\n";
system("cat Lservers_list.txt");

print "\n Is this the list of servers You want to apply chef Heartbeat :- Yes/No : ";
my $input1 = <STDIN>;

if($input1 =~ /yes/i){

print "\n Enter key for the servers : ";
my $key = <STDIN>;
chomp $key;

my $count1 = 1;

open my $file1_in, '<', 'Lservers_list.txt' or die $!;
while(<$file1_in>){
        my $count1=$count1++;
        my @columns = split /\ /, $_;

        my $data11 = "$columns[0]";
        chomp $data11;
        my $data21 = "$columns[1]";
        chomp $data21;

print "\n\nAssign Heartbeat recipe for server $count1 : $data11 \n";

system ("ssh -o StrictHostKeyChecking=no -i ./keys/$key ec2-user\@$data11 -t sudo chef-client ");

}
close $file1_in;
}
}

##################################################################################################

sub bootstrap_windows(){

print "\nList of Windows servers\n";
system("cat Wservers_list.txt");

print "\n Is this the list of servers You want to bootstrap - Yes/No : ";
my $input1 = <STDIN>;

if($input1 =~ /yes/i){

print "\n Enter Administrator user for the servers : ";
my $admin = <STDIN>;
chomp $admin;

my $s1=system ( "stty -echo");
print "\n Enter sudo user password : ";
my $password = <STDIN>;
my $s2=system ( "stty echo");
chomp $password;

print "\n\n Enter Environment name : ";
my $env = <STDIN>;
chomp $env;

my $count = 1;
system ("mkdir -p ./bootstrap_logs/$env");
my $logpath = "./bootstrap_logs/$env";

open my $file_in, '<', 'Wservers_list.txt' or die $!;
while(<$file_in>){
        my $count=$count++;
        my @columns = split /\ /, $_;

        my $data1 = "$columns[0]";
        chomp $data1;
        my $data2 = "$columns[1]";
        chomp $data2;

################# Bootstrap Windows servers ################

print "\n\nBootstrap for server $count : $data1 \n";
my $cmd1=system ("knife bootstrap windows winrm $data1 -x $admin -E $env -N $data2 -P $password  2>&1 | tee $logpath/$data1.logerr & ");

}
close $file_in;
}
}

##################################################################################

sub windows_chef_heartbeat(){

print "\nList of Windows servers\n";
system("cat Wservers_list.txt");

print "\n Is this the list of servers You want to apply chef bootstrap - Yes/No : ";
my $input1 = <STDIN>;

if($input1 =~ /yes/i){

print "\n Enter Administrator user for the servers : ";
my $admin = <STDIN>;
chomp $admin;

my $s1=system ( "stty -echo");
print "\n Enter sudo user password : ";
my $password = <STDIN>;
my $s2=system ( "stty echo");
chomp $password;

my $count1 = 1;

open my $file1_in, '<', 'Wservers_list.txt' or die $!;
while(<$file1_in>){
        my $count1=$count1++;
        my @columns = split /\ /, $_;

        my $data11 = "$columns[0]";
        chomp $data11;
        my $data21 = "$columns[1]";
        chomp $data21;

################# Apply Windows Chef client recipe ################

print "\n\nAssign recipe for server $count1 : $data11 \n";

system ("knife winrm 'name:$data21' 'chef-client -r 'OPS-Windows-chef-client-service'' -x $admin -P $password -a ipaddress -VV");

}
close $file1_in;
}
}
#################################################################################


main ();
0(root@chefws1 /home/engineering/bootstrap)$
