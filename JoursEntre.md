package DDG::Goodie::JoursEntre;
# ABSTRACT: Donne le nombre de jours entre deux dates données.

use DDG::Goodie;
use Date::Calc qw( Date_to_Days); 
use Time::localtime;

triggers start => "jours", "joursentre", "jours_entre";

zci is_cached => 1;
zci answer_type => "jours_entre";

primary_example_queries 'jours entre 01/31/2000 01/31/2001';
secondary_example_queries 'jours entre 01/31/2000 01/31/2001 inclusive';
description 'calcule le nombre de jours entre deux dates';
name 'JoursEntre';
code_url 'https://github.com/duckduckgo/zeroclickinfo-goodies/blob/master/lib/DDG/Goodie/DaysBetween.pm';
category 'calculations';
topics 'everyday';
attribution github => ['http://github.com/JetFault', 'JetFault'];


handle query_lc => sub {

	s/^days(?:\s|_)*between//;
	my @dates = $_ =~ m#([01]?[0-9])/([0-3]?[0-9])/([0-9]{4}(?=\s|$))#g;

	if(scalar(@dates) == 3) {
		my $tm = localtime;
		push(@dates, $tm->mon + 1, $tm->mday, $tm->year + 1900);
	}

	if(scalar(@dates) == 6) {
        my ($days1, $days2);
        my $inclusive = '';

		eval {
			$days1 = Date_to_Days(@dates[2,0,1]);
			$days2 = Date_to_Days(@dates[5,3,4]);
		};
		if ($@) {
			return;
		}
		my $daysBetween = abs($days2 - $days1);
        if(/inclusive/) {
            $daysBetween += 1;
            $inclusive = ', inclusive';
        }
        my $startDate = join '/', @dates[0,1,2];
        my $endDate = join '/', @dates[3,4,5];
		return 'Il y a ' . $daysBetween ." jours entre le ". $startDate . ' et le ' . $endDate . $inclusive . '.';
	}
	return;
};

1;
