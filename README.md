# titanic
naive bayes
Train= readtable('titanic\train.csv');
Test = readtable('titanic\test.csv');
avgage = nanmean(Train.Age);
Train.Age(isnan(Train.Age)) = avgage;
Test.Age(isnan(Test.Age)) = avgage;
X = Train.Age;
for i = 1 : 891
    X(i) = 1.*(X(i)<=15) + 2*(X(i)>15 & X(i)<=45) + 3*(X(i)>45)
end
Train.Age = X; 
X = Test.Age;
for i = 1 : 418
    X(i) = 1.*(X(i)<=15) + 2*(X(i)>15 & X(i)<=45) + 3*(X(i)>45)
end
Test.Age = X; 
table1 = grpstats(Train(:,{'Survived','Sex'}), {'Survived','Sex'});
table2 = grpstats(Train(:,{'Survived','Pclass'}), {'Survived','Pclass'});
table3 = grpstats(Train(:,{'Survived','Age'}), {'Survived','Age'});
for i = 1 : 2
    Pgender1(i) = table1.GroupCount(i+2) / (table1.GroupCount('1_male') + table1.GroupCount('1_female'));
    Pgender0(i) = table1.GroupCount(i) / (table1.GroupCount('0_male') + table1.GroupCount('0_female'));
end
for i = 1 : 3
    Pclass1(i) = table2.GroupCount(i+3) / (table2.GroupCount('1_1') + table2.GroupCount('1_2') + table2.GroupCount('1_3'));
    Pclass0(i) = table2.GroupCount(i) / (table2.GroupCount('0_1') + table2.GroupCount('0_2') + table2.GroupCount('0_3'));
    Page1(i) = table3.GroupCount(i+3) / (table3.GroupCount('1_1') + table3.GroupCount('1_2') + table3.GroupCount('1_3'));
    Page0(i) = table3.GroupCount(i) / (table3.GroupCount('0_1') + table3.GroupCount('0_2') + table3.GroupCount('0_3'));
end

for i = 1 : 418
    P1 = 1;
    P2 = 1;
    if strcmp(Test.Sex{i} ,'male')
       P1 = Pgender1(1) * P1;
       P2 = Pgender0(1) * P2;
    else
       P1 = Pgender1(2) * P1;
       P2 = Pgender0(2) * P2;
    end
    P1 = P1 * (Pclass1(1).*(Test.Pclass(i) == 1) + Pclass1(2).*(Test.Pclass(i) == 2) + Pclass1(3).*(Test.Pclass(i) == 3));
    P2 = P2 * (Pclass0(1).*(Test.Pclass(i) == 1) + Pclass0(2).*(Test.Pclass(i) == 2) + Pclass0(3).*(Test.Pclass(i) == 3));
    P1 = P1 * (Page1(1).*(Test.Age(i) == 1) + Page1(2).*(Test.Age(i) == 2) + Page1(3).*(Test.Age(i) == 3));
    P2 = P2 * (Page0(1).*(Test.Age(i) == 1) + Page0(2).*(Test.Age(i) == 2) + Page0(3).*(Test.Age(i) == 3));
    if P1 > P2
        A(i) = 1
    else
        A(i) = 0
    end
end
